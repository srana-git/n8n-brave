# AI Collections Workflow Configuration Guide

## Prerequisites

Before importing the workflows, ensure you have:
- N8N instance running (v1.0+ recommended)
- Admin access to create credentials
- Access to required external services

## Required Credentials Setup

### 1. MySQL Database (Read-Only)
Create a read-only user for the collections database:

```sql
CREATE USER 'n8n_readonly'@'%' IDENTIFIED BY 'secure_password_here';
GRANT SELECT ON collections_db.* TO 'n8n_readonly'@'%';
FLUSH PRIVILEGES;
```

**N8N Credential Configuration:**
- Credential Type: MySQL
- Name: `mysql_readonly`
- Host: Your MySQL host
- Database: `collections_db`
- User: `n8n_readonly`
- Password: Your secure password
- Port: 3306
- SSL: Recommended for production

### 2. MySQL Database (Write)
For saving reports:

```sql
CREATE USER 'n8n_write'@'%' IDENTIFIED BY 'secure_password_here';
GRANT SELECT, INSERT, UPDATE ON collections_db.collections_reports TO 'n8n_write'@'%';
FLUSH PRIVILEGES;
```

**N8N Credential Configuration:**
- Credential Type: MySQL
- Name: `mysql_write`
- Host: Your MySQL host
- Database: `collections_db`
- User: `n8n_write`
- Password: Your secure password

### 3. Redis Cache
**N8N Credential Configuration:**
- Credential Type: Redis
- Name: `redis_cache`
- Host: Your Redis host
- Port: 6379
- Password: If required
- Database: 0

### 4. OpenAI API
**N8N Credential Configuration:**
- Credential Type: OpenAI API
- Name: `openai_api`
- API Key: Your OpenAI API key
- Organization ID: Optional

### 5. GoHighLevel API
**N8N Credential Configuration:**
- Credential Type: HTTP Header Auth
- Name: `ghl_api`
- Header Name: `Authorization`
- Header Value: `Bearer YOUR_GHL_API_KEY`

### 6. Slack Bot
**N8N Credential Configuration:**
- Credential Type: Slack API
- Name: `slack_bot`
- Access Token: Your bot token (xoxb-...)
- Required Scopes: `chat:write`, `chat:write.public`

## Database Schema Requirements

### customers table
```sql
CREATE TABLE customers (
  id VARCHAR(50) PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100),
  phone VARCHAR(20),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### loans table
```sql
CREATE TABLE loans (
  id INT PRIMARY KEY AUTO_INCREMENT,
  customer_id VARCHAR(50),
  original_amount DECIMAL(10,2),
  due_date DATE,
  status VARCHAR(20),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (customer_id) REFERENCES customers(id)
);
```

### payments table
```sql
CREATE TABLE payments (
  id INT PRIMARY KEY AUTO_INCREMENT,
  loan_id INT,
  amount DECIMAL(10,2),
  payment_date DATE,
  type VARCHAR(20),
  FOREIGN KEY (loan_id) REFERENCES loans(id)
);
```

### current_loans view
```sql
CREATE VIEW current_loans AS
SELECT 
  customer_id,
  SUM(balance) as balance,
  MAX(days_overdue) as days_overdue
FROM loan_details
WHERE status = 'active'
GROUP BY customer_id;
```

### collections_reports table
```sql
CREATE TABLE collections_reports (
  id INT PRIMARY KEY AUTO_INCREMENT,
  report_date DATE,
  messages_sent INT,
  responses_received INT,
  response_rate VARCHAR(10),
  settlements_approved INT,
  payment_commitments INT,
  escalations INT,
  automation_rate VARCHAR(10),
  best_variant VARCHAR(20),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

## GoHighLevel Webhook Configuration

### Cold Outreach Webhook
1. In GoHighLevel, go to Settings > Webhooks
2. Create new webhook:
   - Name: `Collections Cold Outreach`
   - URL: `https://your-n8n-instance.com/webhook/collections/cold-outreach`
   - Method: POST
   - Trigger: When contact enters "Collections" pipeline
   - Headers: Add authentication header if configured

### Response Handler Webhook
1. Create new webhook:
   - Name: `Collections Response Handler`
   - URL: `https://your-n8n-instance.com/webhook/collections/response`
   - Method: POST
   - Trigger: When SMS/Email received
   - Headers: Add authentication header if configured

### Webhook Payload Format
Expected payload for cold outreach:
```json
{
  "customer_id": "CUST123",
  "trigger": "new_collection"
}
```

Expected payload for response handler:
```json
{
  "customer_id": "CUST123",
  "message": "Customer message text",
  "channel": "sms"
}
```

## Slack Channel Setup

Create the following channels in Slack:
- `#collections-activity` - All collection activities
- `#collections-payments` - Payment commitments
- `#collections-settlements` - Settlement negotiations
- `#collections-escalations` - Human intervention required
- `#collections-errors` - System errors
- `#collections-reports` - Daily reports

## Redis Data Structure

### Customer Profile Cache
Key: `customer:{customer_id}:profile`
TTL: 604800 seconds (7 days)
```json
{
  "customer_id": "123",
  "name": "John Doe",
  "phone": "+1234567890",
  "email": "john@example.com",
  "total_loans": 5,
  "total_paid": 2500,
  "current_balance": 400,
  "days_past_due": 45,
  "personality_profile": "payday_payer",
  "settlement_authority": {
    "max_discount": 0.35,
    "min_acceptable": 260,
    "immediate_settlement": 245,
    "payment_plan_months": 3
  }
}
```

### Conversation State Cache
Key: `customer:{customer_id}:conversation`
TTL: 604800 seconds (7 days)
```json
{
  "status": "negotiating",
  "messages": [
    {
      "type": "outbound",
      "text": "Message text",
      "variant": "variant_a",
      "timestamp": "2024-01-01T12:00:00Z"
    }
  ],
  "offers_made": [
    {
      "amount": 275,
      "discount_percent": 31,
      "timestamp": "2024-01-01T12:00:00Z"
    }
  ],
  "strategy_used": "variant_a",
  "last_contact": "2024-01-01T12:00:00Z"
}
```

## Workflow Import Instructions

1. **Import Workflows:**
   - Open N8N interface
   - Go to Workflows > Import
   - Import each JSON file:
     - `cold-outreach-workflow.json`
     - `response-handler-workflow.json`
     - `daily-report-workflow.json`

2. **Activate Workflows:**
   - Open each workflow
   - Click "Activate" toggle
   - Verify webhook URLs are accessible

3. **Test Webhooks:**
   ```bash
   # Test cold outreach
   curl -X POST https://your-n8n.com/webhook/collections/cold-outreach \
     -H "Content-Type: application/json" \
     -d '{"customer_id": "TEST123"}'
   
   # Test response handler
   curl -X POST https://your-n8n.com/webhook/collections/response \
     -H "Content-Type: application/json" \
     -d '{"customer_id": "TEST123", "message": "I can pay 200"}'
   ```

## Environment Variables

Set these in your N8N environment:
```bash
# N8N Configuration
N8N_WEBHOOK_BASE_URL=https://your-n8n-instance.com
N8N_TIMEZONE=America/New_York
N8N_EXECUTION_TIMEOUT=300

# Redis Configuration
REDIS_HOST=your-redis-host
REDIS_PORT=6379
REDIS_PASSWORD=your-redis-password

# MySQL Configuration
MYSQL_HOST=your-mysql-host
MYSQL_PORT=3306
MYSQL_DATABASE=collections_db

# API Keys (store securely)
OPENAI_API_KEY=sk-...
GHL_API_KEY=ghl_...
SLACK_BOT_TOKEN=xoxb-...
```

## Testing & Validation

### 1. Database Connectivity Test
```sql
-- Run as n8n_readonly user
SELECT COUNT(*) FROM customers;
SELECT COUNT(*) FROM loans;
SELECT COUNT(*) FROM payments;
```

### 2. Redis Connectivity Test
```bash
redis-cli ping
redis-cli set test:key "test_value" EX 60
redis-cli get test:key
```

### 3. OpenAI API Test
```bash
curl https://api.openai.com/v1/models \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### 4. Slack Test
Send test message to each channel to verify bot permissions.

## Performance Optimization

### MySQL Indexes
```sql
CREATE INDEX idx_customer_id ON loans(customer_id);
CREATE INDEX idx_loan_id ON payments(loan_id);
CREATE INDEX idx_due_date ON loans(due_date);
CREATE INDEX idx_status ON loans(status);
```

### Redis Configuration
```conf
maxmemory 2gb
maxmemory-policy allkeys-lru
save 900 1
save 300 10
save 60 10000
```

### N8N Settings
- Enable execution pruning
- Set execution data retention to 7 days
- Enable webhook authentication
- Use queue mode for high volume

## Monitoring & Alerts

### Health Checks
- Monitor webhook response times
- Check Redis memory usage
- Monitor OpenAI API usage and costs
- Track MySQL query performance

### Key Metrics to Track
- Webhook success rate
- Average workflow execution time
- OpenAI API token usage
- Redis hit/miss ratio
- Error rate by workflow

## Troubleshooting

### Common Issues

1. **Webhook not triggering:**
   - Check N8N webhook URL is accessible
   - Verify authentication headers
   - Check N8N logs for incoming requests

2. **MySQL connection errors:**
   - Verify credentials and permissions
   - Check network connectivity
   - Ensure user has SELECT permissions

3. **Redis connection issues:**
   - Check Redis is running
   - Verify password if set
   - Check max connections limit

4. **OpenAI API errors:**
   - Verify API key is valid
   - Check rate limits
   - Monitor token usage

5. **Slack message failures:**
   - Verify bot is in channels
   - Check bot permissions
   - Ensure channel names are correct

## Security Best Practices

1. **Credentials:**
   - Use N8N's built-in credential encryption
   - Rotate API keys regularly
   - Use separate credentials for prod/dev

2. **Database:**
   - Use read-only users where possible
   - Implement IP whitelisting
   - Enable SSL connections

3. **Webhooks:**
   - Implement webhook authentication
   - Use HTTPS only
   - Validate payload structure

4. **Redis:**
   - Set strong password
   - Disable dangerous commands
   - Use SSL if exposed to network

## Compliance Considerations

- Ensure FDCPA compliance in all messages
- Implement opt-out handling
- Log all customer interactions
- Maintain audit trail for settlements
- Respect communication time restrictions
- Handle disputes according to regulations

## Support & Maintenance

- Weekly review of message performance
- Monthly prompt optimization
- Quarterly compliance audit
- Regular backup of workflow configurations
- Monitor and update AI model versions

---

*Last Updated: January 2024*
*Version: 1.0.0*