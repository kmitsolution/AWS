# What is an API?

Before understanding API Gateway, let's understand **why APIs are needed**.

Imagine you have an application with:

- Frontend (Web/Mobile App)
- Backend (EC2, Lambda, Containers)
- Database

Users should **never directly access the database or backend servers**.

Instead, they communicate through APIs.

```text
User
  |
  v
API Request
  |
  v
Backend Application
  |
  v
Database
```

Example:

```text
GET /employees/101
```

The client asks:

> "Give me employee 101 details"

The backend processes the request and returns JSON:

```json
{
  "id": 101,
  "name": "John",
  "department": "IT"
}
```

---

# Problems Without API Gateway

Suppose you have multiple backend services:

```text
Mobile App
Web App
Partner App
     |
     |
-------------------
| Service A       |
| Service B       |
| Service C       |
-------------------
```

Each service must handle:

- Authentication
- Authorization
- SSL Certificates
- Rate Limiting
- Logging
- Monitoring
- Request Routing
- DDoS Protection

Every service repeats the same work.

This becomes difficult to manage.

---

# What is AWS API Gateway?

AWS API Gateway is a **fully managed service that acts as the front door for your APIs**.

```text
Client
   |
   v
API Gateway
   |
   +------> Lambda
   |
   +------> EC2
   |
   +------> ECS
   |
   +------> External API
```

API Gateway receives requests and forwards them to the appropriate backend.

Think of it as a **receptionist**:

```text
Customer enters office
         |
         v
Receptionist
         |
         +--> HR
         +--> Finance
         +--> IT
```

The receptionist decides where the request should go.

---

# What Problems Does API Gateway Solve?

## 1. Single Entry Point

Without API Gateway:

```text
app.company.com
auth.company.com
orders.company.com
payments.company.com
```

Clients must know many URLs.

With API Gateway:

```text
api.company.com
```

Everything comes through one endpoint.

---

## 2. Authentication & Authorization

API Gateway can integrate with:

- IAM
- Cognito
- Lambda Authorizers
- JWT Tokens

```text
Client
   |
 Token Validation
   |
API Gateway
   |
Backend
```

Backend doesn't need to validate tokens repeatedly.

---

## 3. Rate Limiting

Prevent abuse.

Example:

```text
User A -> 1000 requests/sec
```

API Gateway can limit:

```text
100 requests/sec
```

Protecting backend resources.

---

## 4. Monitoring

Integrated with:

- Amazon CloudWatch

You can track:

- API calls
- Errors
- Latency
- Throttling

---

## 5. SSL/TLS

API Gateway automatically provides HTTPS support.

```text
https://api.company.com
```

No need to manage web servers.

---

## 6. Serverless Integration

Perfect with:

- AWS Lambda

```text
Client
  |
API Gateway
  |
Lambda
```

No EC2 required.

---

# API Gateway Types

AWS API Gateway supports four major API styles:

```text
API Gateway
    |
    +--> HTTP API
    |
    +--> REST API
    |
    +--> Private REST API
    |
    +--> WebSocket API
```

---

# 1. HTTP API

Newest and cheapest option.

Designed for:

- Lambda
- Microservices
- Simple APIs

```text
Client
   |
HTTP API
   |
Lambda
```

Example:

```text
GET /employees
POST /employees
```

Uses standard HTTP methods.

### Features

✅ JWT Authentication

✅ OIDC Support

✅ Lambda Integration

✅ Low Latency

✅ Lowest Cost

### Limitations

❌ Fewer advanced features than REST API

---

## When to Use

Modern applications.

```text
Mobile App
Web App
Microservices
```

Most new projects should start with HTTP API.

---

# 2. REST API

Older but feature-rich API Gateway type.

```text
Client
   |
REST API
   |
Backend
```

Supports advanced enterprise features.

### Features

✅ API Keys

✅ Usage Plans

✅ Request Validation

✅ Caching

✅ Request Transformation

✅ Response Transformation

✅ WAF Integration

✅ Lambda Authorizers

---

## Example

```text
GET /employees
POST /employees
PUT /employees/101
DELETE /employees/101
```

---

## When to Use

Use REST API if you need:

- API Keys
- Usage Plans
- Request Mapping
- Advanced Transformations

---

# HTTP API vs REST API

| Feature | HTTP API | REST API |
|----------|----------|----------|
| Cost | Lower | Higher |
| Performance | Faster | Slightly Slower |
| API Keys | No | Yes |
| Usage Plans | No | Yes |
| Request Validation | Basic | Advanced |
| Transformations | Limited | Advanced |
| Recommended for New Apps | Yes | Only if advanced features needed |

### Exam Tip

```text
Need cheapest + fastest?
      |
      v
HTTP API
```

```text
Need API Keys, Usage Plans, Caching?
      |
      v
REST API
```

---

# 3. Private REST API

A special REST API that is **not accessible from the Internet**.

Only resources inside a VPC can access it.

```text
EC2
 |
 VPC Endpoint
 |
Private REST API
 |
Backend
```

No public endpoint exists.

---

## Why Use It?

For internal applications.

Example:

```text
HR System
Finance System
Inventory System
```

Only internal servers should access them.

---

## Architecture

```text
Private Subnet EC2
        |
        v
Interface VPC Endpoint
        |
        v
Private REST API
```

Traffic never leaves AWS network.

---

# Public REST API vs Private REST API

## Public REST API

```text
Internet
   |
REST API
   |
Backend
```

Anyone can reach it (subject to authentication).

---

## Private REST API

```text
VPC
 |
Private REST API
```

Only resources inside VPC can access it.

---

# 4. WebSocket API

Normal APIs are request-response.

Example:

```text
Client -> Request
Server -> Response
Connection Closed
```

For real-time communication, this is inefficient.

---

## Example: Chat Application

Without WebSocket:

```text
Client asks every second:

Any new message?
Any new message?
Any new message?
```

This is called polling.

Wasteful.

---

## With WebSocket

```text
Client <=====> Server
```

Single connection remains open.

Server can push data anytime.

---

## Real-time Examples

- Chat applications
- Live stock prices
- Gaming
- Sports scores
- Notifications
- IoT devices

---

## Architecture

```text
Client
   ^
   |
   |
WebSocket API
   |
Backend
```

Messages can flow in both directions.

---

# Complete Comparison

| Feature | HTTP API | REST API | Private REST API | WebSocket API |
|----------|----------|----------|----------|----------|
| Protocol | HTTP | HTTP | HTTP | WebSocket |
| Public Access | Yes | Yes | No | Yes |
| Real-Time | No | No | No | Yes |
| Cheapest | Yes | No | No | No |
| API Keys | No | Yes | Yes | No |
| Usage Plans | No | Yes | Yes | No |
| VPC Only | No | No | Yes | No |
| Best For | Modern APIs | Enterprise APIs | Internal APIs | Chat/Real-Time |

# Easy Memory Trick

```text
HTTP API
   = Fast + Cheap + Modern

REST API
   = Feature Rich

Private REST API
   = Internal Only

WebSocket API
   = Real-Time Communication
```

# Real AWS Architecture Example

```text
Mobile App
     |
     v
HTTP API Gateway
     |
     +----> Lambda (User Service)
     |
     +----> Lambda (Order Service)
     |
     +----> Lambda (Payment Service)
```

For an internal banking system:

```text
Internal EC2
      |
VPC Endpoint
      |
Private REST API
      |
Backend Services
```

For a chat application:

```text
Users
   |
WebSocket API
   |
Lambda
   |
DynamoDB
```

For AWS certification exams, remember:

1. **HTTP API = preferred for new serverless APIs**
2. **REST API = advanced features required**
3. **Private REST API = accessible only through VPC endpoints**
4. **WebSocket API = real-time bidirectional communication**
