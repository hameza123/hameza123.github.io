+++ 
draft = false
date = 2025-09-13T18:15:30+01:00
title = "Day 25: osTicket + ELK Integration"
description = "SOC Analyst Challenge, Day 25"
slug = "SOC Analyst Challenge, Day 25"
authors = "Seclice"
tags = ["SOC", "cybersecurity", "SOC Analyst", "ELK", "challenge"]
categories = ["SOC",""]
series = []
+++

## Day 25:  osTicket + ELK Integration
Ressources :[MyDFIR Channel](https://www.youtube.com/@MyDFIR/)

i went to ip@osticket-serve/upload/scp/index.php/ > admin panel > manage > add API named elastic connector and i copied the key

i went to elasticsearch > stack management > conector > free trial > webhook > configuration > post http://ip@osticket-serve/osticket/upload/api/tickets.xml , authentication none , header key : X-API-Key, value : the copied keycopied > test the body test from https://github.com/osTicket/osTicket/blob/develop/setup/doc/api/tickets.md and run 
the result shoud appear in the aget panel

 
## Step-by-Step Configuration Check

### 1. osTicket API Configuration
- **API Endpoint**: `http://[your-osticket-ip]/osticket/upload/api/tickets.xml`
- **Authentication**: `X-API-Key` header with your copied key
- **Method**: POST

### 2. Elasticsearch Webhook Connector Configuration
```json
{
  "url": "http://your-osticket-ip/osticket/upload/api/tickets.xml",
  "method": "POST",
  "headers": {
    "X-API-Key": "your-copied-api-key",
    "Content-Type": "application/xml"
  }
}
```

### 3. Test Body (XML Format for osTicket)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<ticket>
    <name>John Doe</name>
    <email>john@example.com</email>
    <subject>Test Ticket from Elasticsearch</subject>
    <message>This is a test ticket created via Elasticsearch webhook connector.</message>
    <topic>General Support</topic>
    <priority>Normal</priority>
</ticket>
```

## Common Issues and Solutions

### 1. **URL Format Issues**
- Remove `@` from IP: Use `http://192.168.1.100/` instead of `ip@osticket-serve`
- Ensure proper path: `/osticket/upload/api/tickets.xml`

### 2. **Permission Issues**
- Check osTicket API is enabled in admin panel
- Verify the API key has proper permissions
- Ensure the "elastic connector" API is active

### 3. **Testing the Connection**

**Using curl to test directly:**
```bash
curl -X POST \
  http://your-osticket-ip/osticket/upload/api/tickets.xml \
  -H "X-API-Key: your-api-key" \
  -H "Content-Type: application/xml" \
  -d '<?xml version="1.0"?><ticket><name>Test User</name><email>test@example.com</email><subject>Test</subject><message>Test message</message></ticket>'
```

### 4. **Elasticsearch Alert Configuration**

For creating alerts that trigger tickets:

```json
{
  "alert": {
    "name": "Critical Error - Create osTicket",
    "severity": "high",
    "condition": {
      "script": {
        "source": "ctx.results[0].hits.total.value > 0",
        "lang": "painless"
      }
    },
    "actions": [
      {
        "name": "create-osticket",
        "destination_id": "your-webhook-connector-id",
        "message_template": {
          "source": "<?xml version='1.0'?><ticket><name>Elasticsearch Alert</name><email>alerts@company.com</email><subject>Alert: {{ctx.alert.name}}</subject><message>Alert triggered at {{ctx.execution_time}}. Condition: {{ctx.condition}}</message><priority>High</priority></ticket>",
          "lang": "mustache"
        }
      }
    ]
  }
}
```

## Verification Steps

1. **Check osTicket API Logs** (if enabled)
2. **Test with simple XML first**
3. **Verify Elasticsearch connector response**
4. **Check osTicket admin panel for created tickets**

## Troubleshooting Questions

- Are you getting any specific error messages?
- Does the direct curl test work?
- Is the osTicket API endpoint accessible from your Elasticsearch instance?
- Have you checked osTicket's error logs?

Would you like me to help you troubleshoot a specific error, or would you prefer guidance on setting up the alert rules in Elasticsearch?