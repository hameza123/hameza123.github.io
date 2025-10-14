+++
date = '2025-10-05T12:36:43+01:00'
draft = true
title = "Keycloak : Part 3"
description = "A practical introduction to Keycloak OAuth 2.0 grant flows with real examples and security analysis"
tags = ["keycloak", "oauth2", "security", "iam", "authentication"]
slug = ""
categories = ["cybersecurity", "tutorial"]
series = []
+++


# Intro-Lab: OAuth 2.0 Authorization Grant Flows

## Lab Objectives
- Understand and practice OAuth 2.0 Client Credentials Grant
- Understand and practice Resource Owner Password Credentials Grant
- Analyze token responses and JWT contents
- Learn when to use each grant type

> **Prerequisites**: Basic understanding of authentication concepts, Keycloak running locally, and curl installed.

In this lab, we'll explore three fundamental OAuth 2.0 grant types using Keycloak, complete with real token examples and security analysis.

## 🚀 Lab Setup

First, ensure Keycloak is running with our workshop realm:

```bash
# Start Keycloak in development mode
cd keycloak-21.0.0/bin
./kc.sh start-dev

# Access at: http://localhost:8080
```
---

## Section 1: Client Credentials Grant

### What is Client Credentials Grant?
- **Use Case**: Machine-to-machine communication
- **When to Use**: Service accounts, backend services, API-to-API communication
- **No User Involved**: The client authenticates itself, not on behalf of a user
- **Security**: Uses client ID and client secret

### Step 1: Execute Client Credentials Grant

```bash
curl -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials&client_id=library-client&client_secret=9584640c-3804-4dcd-997b-93593cfb9ea7" \
  http://localhost:8080/realms/workshop/protocol/openid-connect/token
```

### Step 2: Analyze the Response

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJscF9GY01aOEQ3VTZFRVVDaVp5V0FGMjFOY3dqWF9kZHdKNWEzZUNQTXdRIn0...",
  "expires_in": 300,
  "refresh_expires_in": 0,
  "token_type": "Bearer",
  "not-before-policy": 1571836504,
  "scope": "library_curator email profile"
}
```

### Step 3: Decode and Analyze the JWT

Let's break down the decoded [JWT](https://www.jwt.io/) payload: 

```json
{
  "exp": 1759663790,
  "iat": 1759663490,
  "jti": "b9b8b1fa-83a7-4ccd-8996-19d458505cd6",
  "iss": "http://localhost:8080/realms/workshop",
  "aud": ["library-service", "account"],
  "sub": "0cf623f4-a696-4452-b3c2-85a7b4e3d74b",
  "typ": "Bearer",
  "azp": "library-client",
  "allowed-origins": ["http://localhost:9090"],
  "realm_access": {
    "roles": ["library_curator", "offline_access", "uma_authorization"]
  },
  "resource_access": {
    "account": {
      "roles": ["manage-account", "manage-account-links", "view-profile"]
    }
  },
  "scope": "library_curator email profile",
  "clientHost": "127.0.0.1",
  "email_verified": false,
  "clientId": "library-client",
  "preferred_username": "service-account-library-client",
  "email": "service-account-library-client@placeholder.org"
}
```

### Key Observations - Client Credentials
- **Subject**: `service-account-library-client` (not a human user)
- **Roles**: `library_curator` - appropriate for service accounts
- **Token Lifetime**: 300 seconds (5 minutes)
- **No Refresh Token**: `refresh_expires_in: 0`
- **Audience**: `library-service` - intended for the library API

**Security Notes**: 
- ✅ No user credentials exposed
- ✅ Simple for automated processes
- ⚠️ Protect client secrets carefully


---


## 2. Resource Owner Password Credentials Grant

### Use Case: Legacy Application Migration

**When to use**: Trusted first-party applications where redirect flows aren't feasible.

**Execute the flow**:
```bash
curl -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=password&username=bwayne&password=wayne&client_id=library-client&client_secret=9584640c-3804-4dcd-997b-93593cfb9ea7" \
  http://localhost:8080/realms/workshop/protocol/openid-connect/token

answer:

  {"access_token":"eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJscF9GY01aOEQ3VTZFRVVDaVp5V0FGMjFOY3dqWF9kZHdKNWEzZUNQTXdRIn0.eyJleHAiOjE3NTk2NjQ5OTEsImlhdCI6MTc1OTY2NDY5MSwianRpIjoiZjhlZWJjY2EtZDY0NS00OGY0LWFiYzgtY2ZkZGJjNmM3ZDY3IiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwL3JlYWxtcy93b3Jrc2hvcCIsImF1ZCI6WyJsaWJyYXJ5LXNlcnZpY2UiLCJhY2NvdW50Il0sInN1YiI6ImUyMjdkODc5LTExMTYtNGI5YS04MWZmLTM2N2UwYmFkN2EwZSIsInR5cCI6IkJlYXJlciIsImF6cCI6ImxpYnJhcnktY2xpZW50Iiwic2Vzc2lvbl9zdGF0ZSI6ImZmYTUwMDY0LTU1YmItNDU0Mi1iOTBhLTg0ZGVmZGU4ZDdjYSIsImFsbG93ZWQtb3JpZ2lucyI6WyJodHRwOi8vbG9jYWxob3N0OjkwOTAiXSwicmVhbG1fYWNjZXNzIjp7InJvbGVzIjpbImxpYnJhcnlfdXNlciIsIm9mZmxpbmVfYWNjZXNzIiwidW1hX2F1dGhvcml6YXRpb24iXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6ImxpYnJhcnlfdXNlciBlbWFpbCBwcm9maWxlIiwic2lkIjoiZmZhNTAwNjQtNTViYi00NTQyLWI5MGEtODRkZWZkZThkN2NhIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsIm5hbWUiOiJCcnVjZSBXYXluZSIsImdyb3VwcyI6WyJsaWJyYXJ5X3VzZXIiXSwicHJlZmVycmVkX3VzZXJuYW1lIjoiYndheW5lIiwiZ2l2ZW5fbmFtZSI6IkJydWNlIiwiZmFtaWx5X25hbWUiOiJXYXluZSIsImVtYWlsIjoiYnJ1Y2Uud2F5bmVAZXhhbXBsZS5jb20ifQ.PqJsi86vPNsv-WMt7vsnB1O8URbg0PwIYIw83yN5fB-ew05LDX5qgb64ZvjcyA7Nnor0q03009NRCBh9hlN_XfbIsNo1VBClue7QpSYzdtevCn1ZZq2HkQFPILC1oAIudT6VUYvTmLv1hrGKtxXaNagKfxy425gP1DehmlbCBzeYmLr8kCQ9F4MuiRk5bP8h7QbP5f2B9eqj1Kq60p5pWCbRpiMenv4IB6AdpLKpfL6-DzeGVPpbGDA-mKxw2a6SLHwu3F2WL2S5IOtJcRHoO1Cixb2Zynef8qznYr35T0dPvgUUg_OOyFeAm9tY3r6eEDLWnhRmLLqKzcjf-ZEo4w","expires_in":300,"refresh_expires_in":1800,"refresh_token":"eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI0MzY0YTU1Ni1mMDY3LTRjMTYtYmQ0MS0zZDNlNDkwMzA4ZGEifQ.eyJleHAiOjE3NTk2NjY0OTEsImlhdCI6MTc1OTY2NDY5MSwianRpIjoiMDY2YzA0YjAtY2RkZS00OGM5LWI0ZDEtNDk0ZDUwMDgwNjhiIiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwL3JlYWxtcy93b3Jrc2hvcCIsImF1ZCI6Imh0dHA6Ly9sb2NhbGhvc3Q6ODA4MC9yZWFsbXMvd29ya3Nob3AiLCJzdWIiOiJlMjI3ZDg3OS0xMTE2LTRiOWEtODFmZi0zNjdlMGJhZDdhMGUiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoibGlicmFyeS1jbGllbnQiLCJzZXNzaW9uX3N0YXRlIjoiZmZhNTAwNjQtNTViYi00NTQyLWI5MGEtODRkZWZkZThkN2NhIiwic2NvcGUiOiJsaWJyYXJ5X3VzZXIgZW1haWwgcHJvZmlsZSIsInNpZCI6ImZmYTUwMDY0LTU1YmItNDU0Mi1iOTBhLTg0ZGVmZGU4ZDdjYSJ9.lziBfHQzo2L-qV-eaJ0SUsGmt8qxahX6J5cvT2_Nit8","token_type":"Bearer","not-before-policy":1571836504,"session_state":"ffa50064-55bb-4542-b90a-84defde8d7ca","scope":"library_user email profile"}

```

**Key Differences from Client Credentials**:

| Aspect | Client Credentials | Password Grant |
|--------|-------------------|----------------|
| **Subject** | Service Account | User ID |
| **User Info** | Basic service data | Full profile |
| **Refresh Token** | Not provided | 30-minute expiry |
| **Security** | No user credentials | Client sees passwords |

**User Profile in Token**:
```json
{
  "name": "Bruce Wayne",
  "preferred_username": "bwayne",
  "given_name": "Bruce",
  "family_name": "Wayne", 
  "email": "bruce.wayne@example.com"
}
```

**Security Warning**: 
- ❌ Client sees user passwords
- ❌ Vulnerable to phishing
- ✅ Only use for trusted first-party apps
 
--- 
 

## 3. Authorization Code Grant (Recommended)

### Use Case: Modern Web Applications

**When to use**: Web apps, SPAs, mobile applications - this is the **recommended flow** for user authentication.

### Step-by-Step Flow

#### Step 1: Initiate Authorization
Open this URL in your browser:
```bash
http://localhost:8080/realms/workshop/protocol/openid-connect/auth?
  response_type=code&
  client_id=demo-client&
  redirect_uri=http://localhost:9095/client/callback
```

#### Step 2: User Authentication
- Login with: `bwayne` / `wayne`
- You'll see an error (expected - callback URL doesn't exist)

#### Step 3: Extract Authorization Code
From the browser address bar, copy the `code` parameter:
```bash
http://localhost:9095/client/callback?
  session_state=1e242e3b-0cb1-4fef-9ef6-52dabecb1b76&
  code=8be25d0b-c083-4c3e-9fb4-9b4df182ab6d.1e242e3b-0cb1-4fef-9ef6-52dabecb1b76.58f0efec-2e91-4c63-823f-85280996f8a5
```

#### Step 4: Exchange Code for Tokens
```bash
curl -X POST \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code&
      code=8be25d0b-c083-4c3e-9fb4-9b4df182ab6d.1e242e3b-0cb1-4fef-9ef6-52dabecb1b76.58f0efec-2e91-4c63-823f-85280996f8a5&
      redirect_uri=http://localhost:9095/client/callback&
      client_id=demo-client&
      client_secret=b3ec9d3f-d1ee-4a18-b4ba-05d832c15293" \
  http://localhost:8080/realms/workshop/protocol/openid-connect/token

answer:

  "access_token":"eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJscF9GY01aOEQ3VTZFRVVDaVp5V0FGMjFOY3dqWF9kZHdKNWEzZUNQTXdRIn0.eyJleHAiOjE3NTk2NjUzNDgsImlhdCI6MTc1OTY2NTA0OCwiYXV0aF90aW1lIjoxNzU5NjY0OTk1LCJqdGkiOiI0ZGFhZjU3OC05YzE3LTQ5YWUtODM4ZS00Y2FkYTlmYjUyYjEiLCJpc3MiOiJodHRwOi8vbG9jYWxob3N0OjgwODAvcmVhbG1zL3dvcmtzaG9wIiwiYXVkIjoiYWNjb3VudCIsInN1YiI6ImUyMjdkODc5LTExMTYtNGI5YS04MWZmLTM2N2UwYmFkN2EwZSIsInR5cCI6IkJlYXJlciIsImF6cCI6ImRlbW8tY2xpZW50Iiwic2Vzc2lvbl9zdGF0ZSI6IjFlMjQyZTNiLTBjYjEtNGZlZi05ZWY2LTUyZGFiZWNiMWI3NiIsImFsbG93ZWQtb3JpZ2lucyI6WyJodHRwOi8vbG9jYWxob3N0OjkwOTUiXSwicmVhbG1fYWNjZXNzIjp7InJvbGVzIjpbImxpYnJhcnlfdXNlciIsIm9mZmxpbmVfYWNjZXNzIiwidW1hX2F1dGhvcml6YXRpb24iXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6ImVtYWlsIHByb2ZpbGUiLCJzaWQiOiIxZTI0MmUzYi0wY2IxLTRmZWYtOWVmNi01MmRhYmVjYjFiNzYiLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwibmFtZSI6IkJydWNlIFdheW5lIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiYndheW5lIiwiZ2l2ZW5fbmFtZSI6IkJydWNlIiwiZmFtaWx5X25hbWUiOiJXYXluZSIsImVtYWlsIjoiYnJ1Y2Uud2F5bmVAZXhhbXBsZS5jb20ifQ.dLzKqujHpxgJZiPkGe5xRq-_Xhhhs_uIXquDtIHl4qFPoZLFdgrvIacmNlovttWjiQJMf-Yc2C_-Bpx2SRmxRLSohjMwPeTlXAGFushHa5n9timmBJmqJJlxD-GnUzQl8yyPx3QRbEPl2CO9jxHtZWuy-z9HkGBUBF5vQcp4ceIedNSlzHJYd_vF3W-IFlnIVFQOm6igCi0zQdLUIdoVZsoa6bl4dIKr6eXaMb8rsa_LVCVJIDTA5Nn06Gi0JzoVtBR58PVa6eik6NVQVaNt-BXx8O2ervutj1kUqMQD8iNDvSTfR3YsVYn3SRGRXZABAMl-dlGeTEIVApsJoJuZWw","expires_in":300,"refresh_expires_in":1800,"refresh_token":"eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI0MzY0YTU1Ni1mMDY3LTRjMTYtYmQ0MS0zZDNlNDkwMzA4ZGEifQ.eyJleHAiOjE3NTk2NjY4NDgsImlhdCI6MTc1OTY2NTA0OCwianRpIjoiZTBjMWJkMTEtMmM5My00OTc1LTkzZTgtNTkzMWYxODMxYTc1IiwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo4MDgwL3JlYWxtcy93b3Jrc2hvcCIsImF1ZCI6Imh0dHA6Ly9sb2NhbGhvc3Q6ODA4MC9yZWFsbXMvd29ya3Nob3AiLCJzdWIiOiJlMjI3ZDg3OS0xMTE2LTRiOWEtODFmZi0zNjdlMGJhZDdhMGUiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoiZGVtby1jbGllbnQiLCJzZXNzaW9uX3N0YXRlIjoiMWUyNDJlM2ItMGNiMS00ZmVmLTllZjYtNTJkYWJlY2IxYjc2Iiwic2NvcGUiOiJlbWFpbCBwcm9maWxlIiwic2lkIjoiMWUyNDJlM2ItMGNiMS00ZmVmLTllZjYtNTJkYWJlY2IxYjc2In0.1JGrvkJSocbuaquFUq1aAvrZjZA94e7d6uzyQnZ41IM","token_type":"Bearer","not-before-policy":1571836504,"session_state":"1e242e3b-0cb1-4fef-9ef6-52dabecb1b76","scope":"email profile"}

```

**Security Benefits**:
- ✅ Client never sees user credentials
- ✅ Short-lived authorization codes (2 minutes in Keycloak)
- ✅ Supports PKCE for public clients
- ✅ Recommended by OAuth 2.1 specification

## 🔐 Security Best Practices

### Grant Type Selection Guide

| Grant Type | Use Case | Security Level |
|------------|----------|----------------|
| **Authorization Code** | Web apps, mobile apps | ✅✅✅ High |
| **Authorization Code + PKCE** | SPAs, mobile apps | ✅✅✅✅ Highest |
| **Client Credentials** | Service accounts | ✅✅ Medium |
| **Password Grant** | Legacy migration | ✅ Low |

### Key Security Takeaways

1. **Never use Password Grant** for third-party applications
2. **Always validate tokens** in your resource servers
3. **Use short token lifetimes** (5-15 minutes)
4. **Implement proper refresh token rotation**
5. **Store client secrets securely** (never in source code)

 
 

## 📊 Flow Comparison Summary

| Aspect | Client Credentials | Password Grant | Authorization Code |
|--------|-------------------|----------------|-------------------|
| **User Context** | No user | User credentials | User consent |
| **Security** | Medium | Low | High |
| **Use Case** | Service accounts | Legacy apps | Modern apps |
| **Complexity** | Simple | Simple | Moderate |

## 🎯 Conclusion

Understanding OAuth 2.0 grant types is crucial for implementing secure authentication. Key takeaways:

- **Use Authorization Code Flow** for user-facing applications
- **Use Client Credentials** for service-to-service communication  
- **Avoid Password Grant** except for specific legacy scenarios
- **Always consider security implications** of each flow

Keycloak makes implementing these flows straightforward while maintaining security best practices.

---
## ✅ Learning Checkpoints

- Can I explain when to use this grant type?
- Can I identify the security trade-offs?
- Can I execute the curl command successfully?
- Can I decode and understand the JWT contents?


**Next Steps**: In our next post, we'll explore OpenID Connect, social logins, and advanced Keycloak configurations.

 
 
 
