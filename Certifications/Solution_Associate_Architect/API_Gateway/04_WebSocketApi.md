# AWS WebSocket API — Complete Explanation for SAA-C03

## 1. What is a WebSocket API?

Amazon API Gateway WebSocket API enables real-time, two-way communication between a client and a backend over a persistent connection.

Unlike a traditional request-response API, the connection stays open, allowing both sides to send messages whenever needed.

Imagine a WhatsApp chat application. When you send a message, you don't want the application to repeatedly ask the server whether a new message has arrived. You want messages to appear immediately.

That's where WebSocket API helps.

## 2. REST API vs HTTP API vs WebSocket API

### REST API / HTTP API

Client → Request → Server

Server → Response → Request complete

Each request is handled separately. The client typically makes another request to retrieve new information.

### WebSocket API

Client ⇄ WebSocket Connection ⇄ Server

Client sends messages

Server sends messages

One connection can carry many messages in both directions until it closes or disconnects.

## 3. Why do we need WebSocket API?

Consider a food delivery application.

A customer wants to track a delivery partner's location in real time.

### Without WebSocket API: Polling

```
Customer App
     |
     | "Where is the delivery partner?"
     v
    Server
     |
     | "Location: 12.9716, 77.5946"
     v
Customer App

A few seconds later: repeat the request
```

The client repeatedly sends requests, even when the location hasn't changed. This creates unnecessary network traffic and repeated request overhead.

### With WebSocket API

```
Customer App
     ⇄
WebSocket API
     ⇄
Backend Service

Location updates arrive whenever they are available.
```

The backend can send location updates without waiting for the customer to request each one.

Benefits:

- Low-latency, real-time messaging.
- Two-way communication.
- Less repeated request overhead than polling.
- Efficient delivery of frequent updates over an established connection.

WebSocket connections still require connection management, authorization, and reconnection handling. They also have service limits and costs.

## 4. Real-world use cases

[Become a Fullstack Developer](https://images.openai.com/static-rsc-4/T805pm42XfcwCLH8x530_d8NL88KIovyxs1gdzj1jYwYjXrkGjdWyAC7mNPPq8_-bJy8jMvoW0xWJv_j4DQxDYVUx8T6mdu1L0yYYGdwoTdFpZMY_zywF67h7Rfi2sA95SCQQ_f-veEw3n3vT1QhUvTzpuCAr13Xsg9_21CUIxQ?purpose=inline)

Live chat

Messages are sent and received instantly.

[Bel20 sluit onder de 5000 puntengrens | MarketScreener België](https://images.openai.com/static-rsc-4/zPenql64M78teoPq4t2BhwTpyWIqS6Pxqf4nJlAe-6L1rJPYav9FiVeEhyhTtvxiJF8s_qXongowApa2Owec3JAt8kmnPkSS1G87wr-5dcqkK4yYruWxkKwRCJkMTN5VFyDnB7Yq6CQxzjaDo5J4nG5H_EbaP2gukS7HYejXXW0?purpose=inline)

Live dashboards

Price, metrics, or monitoring updates stream to clients.

[Live Order Tracking for Your Food Delivery | Billing Fast](https://images.openai.com/static-rsc-4/8nQ6mA0cjY9WuEyJFP4Trcy3duxB92R_t7r3_oIrWI9a9ZJ5XO6wd7lpvzBsOljQmDJQgtK0vxuOARgFGdLr-_jhkrQ_z75wW484AiE8PWeoiNUw1ul7xiikpcr_sA_RPbyEZBuuKO1XPRl-GrIhTeAhsLcxHqZsIpY9URxIVYY?purpose=inline)

Real-time tracking

Delivery location updates appear without constant polling.

[Secrets Strategies to Grow a Gaming YouTube Channel Fast](https://images.openai.com/static-rsc-4/GkSOUYcwg4KtDLqxgcqx9xGrwAhwLbh2ojNMqB8hax4Nf0bicOsTjeUB0rEHf2rF684luOmcrRusjFIMDjQdF5m822OjYKkmAL4vnYrPDnr-GFFUQEvQno-_HtYom7PJl7AH-skLA3koEGa4LPcnKW4B_gYkAZd1OVNPA-ftiyg?purpose=inline)

Multiplayer gaming

Players exchange game events with low latency.

Other examples include collaborative editing, live notifications, and interactive support applications.

## 5. How AWS WebSocket API works

Let's build a simple chat application using Amazon API Gateway, AWS Lambda, and Amazon DynamoDB.

User A and User B

Browser or mobile application

Amazon API Gateway — WebSocket API

Maintains connections and routes messages

AWS Lambda

Processes chat messages and notifications

Amazon DynamoDB

Stores connection IDs and, if required, chat history

### Step-by-step workflow

1. Connect: User A opens the chat application. The client establishes a WebSocket connection to API Gateway.
2. Register: The `$connect` route can invoke a Lambda function to authorize the connection and record its connection ID.
3. Send message: User A sends a message, which API Gateway routes to a configured message handler.
4. Process: Lambda determines the intended recipient and prepares the response.
5. Deliver: The backend uses the API Gateway Management API to send a message to User B's active connection.
6. Disconnect: When a connection closes, the `$disconnect` route can clean up its stored connection ID.

The persistent WebSocket connection is managed by API Gateway; Lambda does not have to remain running for the entire connection lifetime.

## 6. Important WebSocket routes

API Gateway WebSocket APIs use routes to decide which backend integration handles an incoming message.

| Route                            | Purpose                                          |
| -------------------------------- | ------------------------------------------------ |
| `$connect`                       | Handles a new connection                         |
| `$disconnect`                    | Handles connection termination                   |
| `$default`                       | Handles messages that do not match another route |
| Custom route, e.g. `sendMessage` | Handles a particular application action          |

Example message sent by a client:

```
{
  "action": "sendMessage",
  "recipientId": "user-102",
  "message": "Hello!"
}
```

If the route selection expression is `$request.body.action`, API Gateway uses the `action` value to select the `sendMessage` route.

This is a key exam concept: WebSocket routes let API Gateway direct different message types to different integrations.

## 7. WebSocket API — Supported features

| Feature                               | AWS WebSocket API                              |
| ------------------------------------- | ---------------------------------------------- |
| Two-way communication                 | Yes                                            |
| Persistent connection                 | Yes, subject to connection limits and timeouts |
| Real-time messages                    | Yes                                            |
| Lambda integration                    | Yes                                            |
| Route-based message handling          | Yes                                            |
| API Gateway API keys and usage plans  | No                                             |
| Built-in API Gateway response caching | No                                             |
| Native JWT authorizer like HTTP API   | No                                             |
| Lambda authorizer                     | Yes                                            |
| IAM authorization                     | Yes, including SigV4 where configured          |

For applications using Amazon Cognito or another identity provider, you can validate tokens through a suitable Lambda authorizer or application-level authorization logic. WebSocket API does not have the same native JWT-authorizer configuration as HTTP API.

## 8. WebSocket API vs HTTP API

| Requirement                                       | Best fit      |
| ------------------------------------------------- | ------------- |
| Fetch a product's details                         | HTTP API      |
| Create an order and return its ID                 | HTTP API      |
| Send live chat messages                           | WebSocket API |
| Push live delivery location updates               | WebSocket API |
| Display real-time dashboard updates               | WebSocket API |
| Invoke Lambda through conventional HTTPS requests | HTTP API      |
| Exchange messages continuously in both directions | WebSocket API |

## 9. Key points for AWS SAA-C03

Remember these four points:

1. WebSocket API supports persistent, bidirectional communication.
2. HTTP API and REST API primarily use request-response interactions.
3. Routes such as `$connect`, `$disconnect`, `$default`, and custom routes control message handling.
4. Lambda and DynamoDB are common supporting services for connection management, message processing, and chat applications.

The essential distinction is that API Gateway WebSocket API is designed for applications that need to exchange messages over an established connection, rather than repeatedly making independent HTTP requests.
