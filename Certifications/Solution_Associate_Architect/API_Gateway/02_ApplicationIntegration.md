# AWS Application Integration — Complete Explanation for SAA-C03

Application Integration is an important AWS Solution Architect topic. It explains how different applications, microservices, and AWS services communicate with each other reliably, without becoming tightly dependent on one another.

Your uploaded Application Integration.pdf organizes this module into three phases: API First Approach, Messaging Basics, and Enterprise/Hybrid Integration. It also lists practical architectures such as order processing, fan-out, event-driven automation, and hybrid integration.&#x20;

Application Integration.pdf



Let's understand these concepts step by step, using real-world examples and AWS services.

## 1. What is Application Integration?

Imagine you are building an e-commerce application. It contains multiple services:

- User Service — manages customers.
- Order Service — creates orders.
- Payment Service — processes payments.
- Inventory Service — manages stock.
- Notification Service — sends emails or SMS.

When a customer places an order, these services must communicate.

Customer places an order

Order Service

Creates the order

Payment Service

Processes payment

Inventory Service

Updates stock

Notification Service

Sends order confirmation

Application Integration provides the mechanisms these services use to exchange requests, messages, and events.

### Why is it required?

Without a suitable integration design:

1. One service may have to call several other services directly.
2. If a downstream service is unavailable, the calling service may fail.
3. A sudden increase in orders may overload downstream services.
4. Adding a new consumer may require changes to existing applications.
5. Retrying failed operations and tracking messages becomes difficult.

AWS offers different integration services to address these problems.

## 2. Two fundamental integration approaches

Before learning the individual services, understand the two main communication patterns.

[REST APIs — Beginner Friendly Guide | by Swastik Sharma | Medium](https://images.openai.com/static-rsc-4/0N8vFfZhJwq3eFPXiJ8d_gIP83hhtgaFFEW2AzNXINWE7kEBJmExA4Q41RyAqOK3p0EwMocEr23EkgSdJEZgG48q-xmnKbGDaN-kD4Qyxs-JPpLYKYZrVpP01YYi8V7hkLVXXhvYAlm9_nrR6LVTUYLgvdRx95UoGZgF8hxQxl0?purpose=inline)

A. Synchronous communication

The caller sends a request and waits for a response.

Example: A mobile app requests the details of an order through Amazon API Gateway.

AWS service: API Gateway.

[Learn System Design With Me . Day 12: Message Queue Patterns | by The Latency Gambler | Medium](https://images.openai.com/static-rsc-4/XBxEfW0rK-LoVG_3BTelwM5MLnHUu_X2G0cOvN6DuFQbtZS3FbXP-nxehRQSFDQt6_gCB9nyXtP-KkuPGA7Ovu3reQV56FNhcGrbD4afpT6FyPig0r41APaczi2k4OKF4s_Ynq08MsHQHC75ZlEW9LOlMst8SGOoazSxhZzkjyQ?purpose=inline)

B. Asynchronous communication

The producer sends a message or event and does not need to wait for the consumer to finish processing it.

Example: An order service places an order message in a queue while another service processes it later.

AWS services: SQS, SNS, EventBridge.

Remember: API Gateway is commonly used for request-response communication, while SQS, SNS, and EventBridge support asynchronous integration patterns.

These patterns can be combined in the same application.

## 3. Phase 1 — API First Approach

Your PDF starts with API Gateway, covering REST, HTTP, and WebSocket APIs.&#x20;

Application Integration.pdf



An API is a defined interface through which one application can request data or functionality from another application.

### Amazon API Gateway

[Padrão de arquitetura hexagonal - AWS Orientação prescritiva](https://images.openai.com/static-rsc-4/J9eh0SgC6jbbHvkVSQiSbj0LaI4Gc6UAWFSNywflb1_ui4FGtRazNnvrV0Rde-RQkD2IUEHHndEVoJpwkhivopKsjgmwrW_GLM1GFseGdDd-mASianZhOeEJQ7eBHJor7IkyGSGPP4i9kpFykrSPLN3NlDIEy3g7O7-eMzItWv8?purpose=inline)

Amazon API Gateway provides managed API endpoints for applications and backend services.

It can route requests to Lambda, HTTP endpoints, and other supported integrations, while providing features such as authorization, monitoring, and throttling.

Example:

```
Mobile App
    |
    | HTTPS: GET /orders/101
    v
API Gateway
    |
    v
Lambda Function
    |
    v
DynamoDB
    |
    v
Order Details returned to App
```

When should you use it?

- A mobile application needs to access backend functionality.
- You want to expose a serverless application through HTTPS endpoints.
- You need managed API authentication, request routing, and monitoring.
- You need persistent, bidirectional connections for real-time applications using WebSocket APIs.

For the detailed differences between HTTP API, REST API, Private REST API, and WebSocket API, refer to the preceding explanation in this conversation.

SAA-C03 exam clue: If the question asks you to expose a Lambda function through a managed HTTPS API, consider API Gateway.

## 4. Phase 2 — Messaging Basics

This is where AWS application integration becomes particularly important for microservices and distributed systems.

### A. Amazon SQS — Simple Queue Service

[The Basics of AWS SQS, CLI Operations, and Example Code for Producing and Consuming Messages | by ETL, ELT, Data and AI/ML | Medium](https://images.openai.com/static-rsc-4/i1wZYsvU0kh0EwRBqXOqTjLEC4H8HvB4kcisGkjsTEhZGOf6qPll0igrJu7KSXW0D-y6QyHeaF2OkWy4_zeKyWlQDpbSU2IP1vsu2O367AbQxAtGev9x3fEzac9wOecVos5IvofuXlcweuz9ITa5oz8EGvqXJfb0xr1PFaNj8dU?purpose=inline)

Amazon SQS is a managed message queue.

It temporarily holds messages until consumers retrieve and process them. This allows producers and consumers to work independently.

#### What problem does SQS solve?

Suppose your e-commerce application receives 10,000 orders during a sale. Your order-processing service can accept messages faster than the payment service can process them.

Without a queue:

```
Order Service ---> Payment Service
                        |
                 Service overloaded
```

With SQS:

```
Order Service
      |
      v
   Amazon SQS
      |
      v
Payment Worker
      |
      v
 Process Order
```

The queue buffers incoming work. Consumers process messages at a manageable rate.

The order service does not need to wait for payment processing to finish before submitting a message.

### How SQS works

1. A producer sends a message to a queue.
2. SQS stores the message until it is received or expires.
3. A consumer receives and processes the message.
4. After successful processing, the consumer deletes the message.

If processing fails, the message can become available again after the visibility timeout. A dead-letter queue (DLQ) can isolate messages that repeatedly fail, subject to the configured redrive policy.

### Types of SQS queues

| Feature          | Standard Queue                                    | FIFO Queue                                                                                           |
| ---------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| Throughput       | Very high, nearly unlimited within service quotas | Lower than Standard, with higher-throughput modes available                                          |
| Message ordering | Best-effort ordering                              | Strict ordering within a message group                                                               |
| Delivery         | At least once; duplicates are possible            | Exactly-once processing support within its deduplication scope; consumers should still be idempotent |
| Typical use      | Background jobs, image processing                 | Ordered transactions, sequential workflows                                                           |

Example: When a customer uploads a product image, the application sends a message to SQS. A worker later resizes the image and creates thumbnails.

The upload request doesn't need to wait for all image-processing tasks to complete.

Exam clue: Look for buffering, decoupling, asynchronous processing, workload spikes, or background jobs. These often point to SQS.

### B. Amazon SNS — Simple Notification Service

[What is Amazon SNS? - Amazon Simple Notification Service](https://images.openai.com/static-rsc-4/JcPrVsZ0UlYrM_2HSWhHmce7fxbZ2-mbuKsmY6gBJ7K6BC0hBqEuTPSt0r3qn-aSvIDfBxuhrfleHnLvoRjB1yak4ZYrclE2hKzFlpqGpfD77acdkFENx9_R9rZnGHta1-Ykf951hf4Z4JgLkS9WQTCKjozEO3XZjBv39uFLcrc?purpose=inline)

Amazon SNS is a managed publish/subscribe messaging service.

A publisher sends a message to an SNS topic, and SNS delivers it to subscribed endpoints.

#### What problem does SNS solve?

Suppose an order is placed. Multiple systems need to know about it:

- Notification service sends an email.
- Analytics service records the sale.
- Shipping service starts fulfillment.
- Loyalty service awards points.

Without SNS, the order service may need to call all four services directly.

With SNS:

```
              Order Service
                    |
                    v
                 SNS Topic
              /      |      \
             v       v       v
          Email   Analytics Shipping
```

The publisher sends one message to the topic, and SNS distributes it to subscribers.

SNS supports subscribers such as SQS queues, Lambda functions, HTTP/S endpoints, and other supported destinations.

Important: SNS is a notification and message-distribution service. It is not a durable work queue for each consumer in the same way as SQS.

#### Example: Order confirmation

The Order Service publishes:

```
{
  "event": "OrderPlaced",
  "orderId": "ORD-1001",
  "amount": 2500
}
```

SNS distributes the event to its configured subscribers.

Exam clue: Look for one publisher notifying multiple subscribers, push notifications, or pub/sub. These often point to SNS.

### C. SNS + SQS Fan-out Architecture

Your PDF specifically lists SNS + SQS fan-out as a core topic.&#x20;

Application Integration.pdf



Fan-out means one message is distributed to multiple independent consumers.

Why combine SNS and SQS instead of using SNS alone?

Because each consumer may need its own queue to buffer messages and process them independently.

Order Service

Publishes OrderPlaced event

SNS Topic

Distributes the event

SQS Queue 1

Shipping

SQS Queue 2

Analytics

SQS Queue 3

Notifications

#### Why is this architecture powerful?

Imagine the Analytics Service is down for 30 minutes.

- The order service can continue publishing events.
- SNS continues delivering to the configured subscribers when possible.
- The Analytics SQS queue buffers its messages.
- The other consumers can continue processing their own queues.
- Analytics can catch up after recovering, subject to queue retention and delivery configuration.

This reduces coupling and helps prevent one slow consumer from directly blocking all other consumers.

Exam clue: If one event must reach several independent applications, and each application needs reliable buffering and independent processing, consider SNS + SQS fan-out.

### D. Amazon EventBridge

[Salesforce Help](https://images.openai.com/static-rsc-4/WnsIYVWBtd9kFej4GY4slwxSKSxZWTRfVn8JNYHvM8EYckAAAMbLhNAHfzodF-IHtl-gPuBbstRsMRfajw79Reopr3q2hDKHm9FuPKXcnnS9qPBL4ipvEGLtMNKpfrq4KzOBS2UQ43O5NAk-zd_wrrnxNc8atpzG_evk6p44Bjs?purpose=inline)

Amazon EventBridge is a serverless event bus that routes events to targets based on rules.

It is especially useful for event-driven architectures, service integrations, and automation triggered by changes in your environment.

#### What problem does EventBridge solve?

Consider an AWS environment where several events occur:

- An EC2 instance changes state.
- A new object is uploaded to S3.
- An order is placed.
- A scheduled task needs to run.
- A security event requires an automated response.

You don't want every application constantly checking whether something has changed.

Instead, events are sent to an event bus, and rules determine which targets should receive them.

```
AWS Services / Applications
           |
           v
    EventBridge Event Bus
           |
       Match Rules
       /    |     \
      v     v      v
   Lambda  SQS   Step Functions
```

#### Example: Automatic response to an EC2 state change

1. An EC2 instance changes to a stopped state.
2. An event is delivered to EventBridge.
3. An EventBridge rule matches the relevant event.
4. The configured target, such as a Lambda function, runs an appropriate action.

Other use cases include scheduled tasks, application events, and routing events between AWS services and supported SaaS integrations.

#### EventBridge vs SNS

| Feature         | SNS                                      | EventBridge                                           |
| --------------- | ---------------------------------------- | ----------------------------------------------------- |
| Main purpose    | Publish/subscribe notifications          | Event routing and event-driven automation             |
| Routing         | Topics and optional subscription filters | Rules that match event patterns                       |
| Typical trigger | Publisher sends to a topic               | AWS service, application, partner, or scheduled event |
| Best example    | Notify multiple consumers about an order | Run a workflow when a particular AWS event occurs     |

Exam clue: Look for event bus, event pattern matching, AWS service events, scheduled rules, or event-driven automation. These often point to EventBridge.

EventBridge and SNS can overlap in some use cases. Choose based on the routing, filtering, event-source, and integration requirements in the question.

## 5. Phase 3 — Enterprise and Hybrid Integration

Your PDF introduces Amazon MQ and AWS AppSync in this phase.&#x20;

Application Integration.pdf



### A. Amazon MQ

[Amazon MQ for ActiveMQ ブローカーのデプロイオプション - Amazon MQ](https://images.openai.com/static-rsc-4/bQj9BXYP2ojQgmAjC-vgHxbtIwdiWqa_0kYys3dZS36d20N96U_7Si-991N3TOZ4Aomwsm2cwLFD3_-8SiOee8CczqtDJK1m2sD70W9Ab8kIdmIxPYHWsFK7vO5jmYna0kWvKwYrJ0g4hSjc-wNHVc3AMIbUXJqGg4xX_QSNcqw?purpose=inline)

Amazon MQ is a managed message broker service for applications using supported messaging technologies such as Apache ActiveMQ and RabbitMQ.

It is useful when an organization wants to migrate existing messaging applications to AWS without completely redesigning their communication model.

#### What problem does Amazon MQ solve?

Imagine a company has a legacy Java application that already uses ActiveMQ.

The company wants to migrate that application to AWS, but rewriting the application to use Amazon SQS would require significant code changes.

Amazon MQ may provide a more compatible migration path because the application can continue using supported broker protocols and messaging patterns.

```
Legacy Java Application
          |
          | Existing messaging protocol
          v
     Amazon MQ Broker
          |
          v
   Another Application
```

When should you use Amazon MQ?

- Migrating an existing application that uses ActiveMQ or RabbitMQ.
- Preserving compatibility with supported messaging protocols.
- Supporting traditional broker-based messaging patterns.

Exam clue: If a question emphasizes existing ActiveMQ/RabbitMQ applications, protocol compatibility, or minimal application changes, consider Amazon MQ.

Important distinction: SQS is a fully managed AWS queue service, whereas Amazon MQ provides managed brokers for supported messaging technologies. They are not interchangeable in every architecture.

### B. AWS AppSync

[20190821 AWS Black Belt Online Seminar AWS AppSync | PDF](https://images.openai.com/static-rsc-4/CnNCUgkIiXADrp_O46QqxYIJOk0BivvTPkBBYeZfChUw33U9I0upHexB_4NsOwozNMPeQP_Iefs6_j2tVhJEtXA7DPUWeGDnRR2bXt-3bFUntGrD-D3yI5QiyYPW5rzkeHpbjW8c9rXTCEI_5Tq_QZIcANuqOQktLp2gqiGcLJU?purpose=inline)

AWS AppSync is a managed service for building GraphQL APIs and supported real-time data applications.

It allows clients to request the specific data they need from connected data sources.

#### What problem does AppSync solve?

Suppose a mobile application displays a product page containing:

- Product details
- Price
- Available stock
- Customer reviews

Without an appropriate API layer, the application might have to call several backend endpoints and combine the results itself.

AppSync can expose a GraphQL API that brings together data from supported sources, such as DynamoDB and Lambda.

Example GraphQL query:

```
query GetProduct {
  getProduct(id: "101") {
    id
    name
    price
  }
}
```

The client asks for only the fields it needs.

AppSync also supports real-time updates through GraphQL subscriptions and other supported real-time mechanisms.

When should you use AppSync?

- A mobile or web application needs a GraphQL API.
- Multiple data sources need to be combined behind one API.
- Clients need real-time data updates.
- You want managed GraphQL integration with supported AWS data sources.

Exam clue: If the question mentions GraphQL, mobile data synchronization, or real-time GraphQL subscriptions, consider AppSync.

## 6. All AWS Application Integration Services Compared

Use this table as a revision sheet for AWS Solution Architect Associate (SAA-C03).

| AWS service | Main purpose                                          | Remember it as               |
| ----------- | ----------------------------------------------------- | ---------------------------- |
| API Gateway | Expose and manage APIs                                | Front door for API requests  |
| SQS         | Buffer messages for consumers                         | Queue                        |
| SNS         | Distribute notifications to subscribers               | Pub/sub                      |
| SNS + SQS   | Distribute events and buffer work independently       | Fan-out                      |
| EventBridge | Route events using rules                              | Event bus                    |
| Amazon MQ   | Support applications using compatible message brokers | Legacy messaging integration |
| AppSync     | Build GraphQL APIs and real-time data applications    | GraphQL                      |

## 7. Interactive Scenario-Based Revision

Select the AWS service you think best fits each requirement, then check the answer.

1\. A company wants to buffer thousands of order-processing jobs so workers can process them at their own pace.

Amazon SNS

Amazon SQS

Amazon AppSync

2\. One OrderPlaced event must reach shipping, analytics, and notifications, with each consumer processing independently.

SNS + SQS fan-out

API Gateway only

Amazon MQ only

3\. When an AWS resource changes state, a rule should match the event and trigger a Lambda function.

Amazon SQS only

Amazon EventBridge

Amazon AppSync

4\. A company wants to migrate an application that already uses ActiveMQ while minimizing messaging-related code changes.

Amazon MQ

Amazon SNS

API Gateway

5\. A mobile application needs a GraphQL API that retrieves data from DynamoDB and Lambda.

Amazon SQS

Amazon AppSync

Amazon SNS

6\. A company needs a managed HTTPS endpoint to expose backend functionality to mobile clients.

Amazon API Gateway

Amazon SQS

Amazon MQ

0 of 6 answered

Reset quiz

## 8. Real-World Architecture: E-Commerce Application on AWS

Let's combine the concepts into one architecture.

Web or Mobile Application

Amazon API Gateway

Accepts customer requests

AWS Lambda — Order Service

Validates and creates the order

Amazon SNS — Order Events

Distributes the OrderPlaced event

SQS

Payment processing

SQS

Inventory update

SQS

Notifications

Illustrative architecture: SNS fans out to separate SQS queues, with each queue feeding its own consumer. EventBridge could separately route selected application or AWS events to automation targets.

### How the architecture works

1. The customer places an order through the frontend.
2. API Gateway routes the request to the Order Service.
3. The Order Service validates and creates the order.
4. The service publishes an order event to SNS.
5. SNS distributes the event to separate SQS queues.
6. Payment, inventory, and notification consumers process their respective messages independently.

For production systems, you must also design for duplicate deliveries, retries, dead-letter queues, idempotent processing, and failure handling. For example, publishing an event and committing an order to a database may require an outbox pattern to avoid losing events when one operation succeeds and the other fails.

## 9. Final SAA-C03 Memory Map

API Gateway

Client requests, HTTPS APIs, managed API access.

SQS

Queue, buffering, asynchronous work, decoupling.

SNS

Publish/subscribe, notifications, fan-out.

EventBridge

Event buses, filtering, routing, automation.

Amazon MQ

Compatible message brokers and legacy migrations.

AppSync

GraphQL APIs and real-time application data.

The most important exam skill is to identify the problem described in the question rather than choosing a service based only on the words “messaging” or “integration.” Ask yourself: Does the application need an API, a queue, publish/subscribe, event routing, broker compatibility, or GraphQL?

That distinction will help you solve many AWS architecture questions.
