# AWS REST API vs HTTP API — Detailed Explanation for SAA-C03

Your uploaded PDF, REST API vs HTTP API, compares Amazon API Gateway's REST API and HTTP API across authentication, security, traffic control, transformations, performance, monitoring, and integrations.&#x20;

REST API vs HTTP API.pdf



The most important concept is this:

Both REST API and HTTP API handle normal HTTP request-response communication. REST API provides more advanced configuration features, while HTTP API is generally simpler and less expensive.

One clarification: AWS API Gateway supports REST APIs, HTTP APIs, and WebSocket APIs. A private REST API is a deployment configuration of REST API, not a separate fourth API protocol.

## 1. Understand the basic architecture

REST API

Advanced API management and configuration

Client → API Gateway → Backend

Useful when you need features such as API keys, usage plans, caching, request validation, or detailed request and response mapping.

HTTP API

Simpler API development with lower cost

Client → API Gateway → Backend

Useful for modern applications, Lambda integrations, JWT authorization, and HTTP-based microservices when the advanced REST API feature set isn't required.

## 2. Authentication and authorization

Authentication verifies who is calling the API. Authorization determines what that caller is allowed to do.

Your PDF covers four approaches.&#x20;

REST API vs HTTP API.pdf



### A. API keys

An API key identifies a calling application or customer. With REST API, API keys can be used with usage plans to track usage and apply configured throttling and quotas.

Example:

```
Client
   |
   | x-api-key: customer-key
   v
REST API
   |
   v
Backend
```

Scenario: You provide a paid weather API to multiple customers and want to associate their usage with API keys.

- REST API: supports API keys and usage plans.
- HTTP API: doesn't support API Gateway API keys and usage plans in the same way.

Important: API keys are not strong user authentication by themselves. Use an appropriate authorization mechanism to protect sensitive resources.

### B. IAM authentication

AWS Identity and Access Management (IAM) lets authorized AWS principals sign requests using AWS Signature Version 4 (SigV4).

Example:

```
EC2 Instance
    |
    | IAM Role + SigV4
    v
API Gateway
    |
    v
Backend
```

Scenario: An EC2 application calls an API, and you want access controlled through AWS IAM permissions.

- REST API: supports IAM authorization.
- HTTP API: also supports IAM authorization.

The PDF's comparison marks IAM authorization as unsupported for HTTP API; that entry is incorrect. HTTP APIs support IAM authorization.

### C. Amazon Cognito

Amazon Cognito supports user sign-in and issues tokens that applications can use to access protected APIs.

```
Mobile App
    |
    v
Amazon Cognito
    |
    | Access token
    v
API Gateway
    |
    v
Backend
```

Scenario: Customers log in to your e-commerce app before accessing their orders.

Both REST API and HTTP API support Cognito-based authorization, although their authorizer configurations differ.

### D. JWT and OAuth 2.0

JSON Web Tokens (JWTs) carry claims about a user or client. OAuth 2.0 is an authorization framework often used to obtain access tokens.

Example:

```
User signs in
     |
     v
Identity Provider
(Auth0 / Okta)
     |
     v
JWT Access Token
     |
     v
HTTP API → Backend
```

HTTP APIs support native JWT authorizers, including integration with compatible OAuth 2.0/OpenID Connect identity providers.

REST APIs can use Cognito authorizers or Lambda authorizers for token validation and authorization logic. They do not offer the same native JWT-authorizer configuration available for HTTP APIs.

Exam tip: If a question specifically asks for a native JWT authorizer with an OAuth/OIDC identity provider, HTTP API is often the most direct choice.

## 3. Security and traffic control

### A. Throttling and rate limiting

Throttling limits the rate of incoming requests to help protect the backend from overload.

Example: Your API receives a sudden traffic spike during a sale.

```
1000 requests arriving rapidly
            |
            v
       API Gateway
            |
     Throttling limits
            |
            v
  Backend receives controlled traffic
```

Both REST APIs and HTTP APIs support throttling. REST APIs also offer usage-plan-based throttling and quotas for API-key-associated clients.

Remember that throttling is not a guarantee that every request below a configured limit will succeed, nor does it replace backend capacity planning.

### B. AWS WAF integration

AWS WAF helps filter web requests based on configured rules, such as rules designed to block suspicious traffic, common web exploits, or unwanted request patterns.

- REST API: supports direct AWS WAF association.
- HTTP API: does not support direct WAF association in the same way. You can use CloudFront with AWS WAF in front of an HTTP API where the architecture supports it.

Example:

```
Internet
   |
   v
CloudFront + AWS WAF
   |
   v
HTTP API
   |
   v
Backend
```

Exam clue: If the requirement specifies direct AWS WAF association with API Gateway, consider REST API. If you use HTTP API with WAF, evaluate a CloudFront-based design.

### C. Resource policies

Resource policies define which principals, accounts, IP ranges, or networks can invoke an API, subject to the policy's supported conditions.

Example requirement: Allow requests only from an approved corporate IP range.

Both REST APIs and HTTP APIs support resource policies, though the available policy conditions and deployment requirements should be checked for the specific design.

For a private REST API, the combination of a VPC interface endpoint, API resource policy, and appropriate endpoint policy can provide network-level access restrictions.

## 4. Request and response transformation

This is a major difference between REST API and HTTP API.

### A. Request and response mapping

Suppose your frontend sends this JSON:

```
{
  "customerName": "John",
  "phone": "9876543210",
  "unusedField": "ABC"
}
```

But your legacy backend expects:

```
{
  "name": "John",
  "phone": "9876543210"
}
```

With suitable REST API mapping templates, API Gateway can transform the request before forwarding it.

```
Frontend Request
      |
      v
REST API Gateway
      |
      | Transform request
      v
Legacy Backend
```

REST APIs support advanced mapping templates using Velocity Template Language (VTL). HTTP APIs offer more limited parameter mapping capabilities and do not support the same full VTL mapping-template functionality.

Use REST API when: You need substantial request/response transformations without modifying the backend.

### B. Request validation

Request validation checks whether an incoming request meets configured requirements before sending it to the backend.

For example, an API requires a customer ID:

```
{
  "customerId": ""
}
```

If validation rules reject the request, API Gateway can return a client error instead of invoking the backend.

REST APIs support configurable request validation for parameters and request bodies using configured models and validators. HTTP APIs do not offer the same built-in request-validator feature set; validation may need to be implemented in the backend or another component.

### C. Mapping templates

VTL mapping templates can reshape incoming requests or outgoing responses.

Example:

```
Incoming request
      |
      v
VTL Mapping Template
      |
      v
Backend-compatible payload
```

This is particularly useful when integrating older applications that cannot easily change their data format.

| Capability                     | REST API | HTTP API                                              |
| ------------------------------ | -------- | ----------------------------------------------------- |
| Advanced VTL mapping templates | Yes      | No                                                    |
| Built-in request validation    | Yes      | Limited; no equivalent REST request-validator feature |
| Basic parameter mapping        | Yes      | Yes, with more limited capabilities                   |

These distinctions are especially useful for architecture exam questions involving legacy systems.

## 5. Performance, cost, caching, and monitoring

### A. Caching

REST APIs support API Gateway response caching for supported configurations. Caching can reduce backend invocations and latency when many users request the same data.

Example:

```
1000 users request product catalog
                 |
                 v
           REST API Cache
                 |
       +---------+---------+
       |                   |
   Cache hit            Cache miss
       |                   |
       v                   v
 Cached response        Backend
```

HTTP APIs do not provide the equivalent built-in API Gateway stage-cache feature.

Use caching when responses can safely be reused and the freshness requirements allow it. Caching is not appropriate for every endpoint, particularly when users must always receive personalized or up-to-date data.

### B. Cost

HTTP APIs are generally less expensive than REST APIs for comparable request volumes and supported features.

The exact savings depend on the AWS Region, request volume, data transfer, and associated services. The PDF's claim of a fixed 70% saving should be treated as an illustration, not a universal pricing guarantee.

For example, a small application exposing a few Lambda functions may be a good fit for HTTP API if it does not need advanced REST API features.

### C. CloudWatch logs and access logs

Both REST APIs and HTTP APIs integrate with Amazon CloudWatch for logging and monitoring, subject to the logging settings you configure.

Access logs can help you track details such as:

- Request time
- HTTP method
- Request path
- Response status code
- Request identifiers

Example:

```
GET /orders/101  -> 200
POST /orders     -> 201
GET /orders/999  -> 404
```

Execution logging and access logging are different configurations. The exact logging options vary by API type.

## 6. Integration types

Your PDF also compares supported integrations.&#x20;

REST API vs HTTP API.pdf



[File:Amazon Lambda architecture logo.svg - Wikimedia Commons](https://images.openai.com/static-rsc-4/BVOKTuzq-WE23sUpbEYalv9OTNIt2HQXfPQ8L1sXZjnUuDWtKPluZQI3BkSmEgRPi8aSelgujA_oD_KLtmOAqPNc7ACR1HWwYDl9RjAmgG_tzIb3WTa63v82AoORkHpkPFdAQosynUb76ILUymiy2X-6gtS_Pxs0XupFcvL21GE?purpose=inline)

Lambda integration

Both REST APIs and HTTP APIs can invoke Lambda functions.

Example: A registration endpoint invokes Lambda to create a customer record in DynamoDB.

[File:AWS Simple Icons Compute Amazon EC2 Instances.svg - Wikimedia Commons](https://images.openai.com/static-rsc-4/czRTPfSeb0x4UEJCQIfEesy1xtqrZI4vLg2_zSKnidP9eXr9oSaTLmzkd2YCc80dVoYgsIMs6xhh_0iE4SozDxQUVabU3rlfsCt1DIQSv2auMbHAdZNdWKTgAfGAEO6nTQIa9SydLJff7jxLgD748XMu-WtSQpCiGBS9UKa59wA?purpose=inline)

HTTP/URL integration

Both can route requests to supported HTTP backends.

Example: API Gateway forwards a request to a web application hosted on EC2 or an accessible on-premises endpoint.

[AWS Application Load Balancer (ALB) 使用時の注意点 | 大阪のAWSパートナー | ターン・アンド・フロンティア](https://images.openai.com/static-rsc-4/5fbOAgdONQgXRE6Z03PCMu1d7OHXttmaAjCUjdYNd4rw3vtZvvGOtu_VYtbbTJyVMWEWQ4yUBMrYP-H2B7QIrxV3nJfsoziMNAWJ4npbSsEhHGMeENXiLUDeRpU63VwyQZiiE4LfGogFFHbwytR_6ZMWTRPK8oYNRlKtCZB_HhU?purpose=inline)

Application Load Balancer integration

HTTP APIs support private integrations with Application Load Balancers and other supported private resources. This is useful for containerized services running behind an ALB.

REST APIs can use supported HTTP integrations and VPC Link configurations too, but their integration capabilities and configuration differ. The PDF's blanket claim that REST APIs do not support ALB integration is misleading.

Example architecture for an application running on ECS:

```
Client
   |
   v
HTTP API
   |
   v
VPC Link
   |
   v
Internal Application Load Balancer
   |
   v
ECS Service
```

The exact VPC Link type and load-balancer configuration depend on the API type and supported integration design.

## 7. Other features

### A. Custom domain names

Instead of exposing a long default API Gateway URL, you can configure a domain such as:

`https://api.example.com`

Both REST APIs and HTTP APIs support custom domain names, subject to the appropriate certificate, DNS, and API configuration.

### B. CORS

Cross-Origin Resource Sharing (CORS) controls whether a browser-based frontend can make requests to an API hosted on a different origin.

Example:

```
Angular App
http://localhost:4200
        |
        | HTTPS request
        v
API Gateway
https://api.example.com
```

If CORS is not configured appropriately, the browser may block the frontend from accessing the response.

Both REST APIs and HTTP APIs support CORS configuration. The configuration differs by API type, and the backend may also need to return appropriate CORS headers.

### C. Private APIs

A private REST API is accessible through an interface VPC endpoint rather than directly through a public API endpoint.

```
Application in VPC
        |
        v
Interface VPC Endpoint
        |
        v
Private REST API
        |
        v
Backend Service
```

Private REST APIs are useful for internal enterprise applications that should not be publicly accessible.

Important distinction:

- REST API: supports private API endpoints.
- HTTP API: does not support the same API Gateway private endpoint type. However, HTTP APIs can integrate with private backend resources using supported VPC Link configurations.

## 8. Complete REST API vs HTTP API comparison

This table summarizes the capabilities discussed in your PDF, with corrections where needed.

| Feature                        | REST API                                    | HTTP API                           |
| ------------------------------ | ------------------------------------------- | ---------------------------------- |
| Lambda integration             | Yes                                         | Yes                                |
| HTTP backend integration       | Yes                                         | Yes                                |
| IAM authorization              | Yes                                         | Yes                                |
| Cognito authorization          | Yes                                         | Yes                                |
| Native JWT authorizer          | No equivalent HTTP API-style JWT authorizer | Yes                                |
| API keys and usage plans       | Yes                                         | No                                 |
| Basic throttling               | Yes                                         | Yes                                |
| AWS WAF direct association     | Yes                                         | No; CloudFront-based options exist |
| Resource policies              | Yes                                         | Yes                                |
| Advanced VTL mapping templates | Yes                                         | No                                 |
| Built-in request validation    | Yes                                         | More limited                       |
| API Gateway response caching   | Yes                                         | No equivalent stage-cache feature  |
| CloudWatch logging             | Yes                                         | Yes                                |
| Custom domain                  | Yes                                         | Yes                                |
| CORS                           | Yes                                         | Yes                                |
| Private API endpoint type      | Yes                                         | No                                 |
| Relative cost                  | Generally higher                            | Generally lower                    |

## 9. Which API should you choose?

Select your application requirement:

Simple API with Lambda, JWT authorization, and lower cost

API keys, customer usage plans, and quotas

Advanced VTL mapping and request validation

Built-in API Gateway response caching

An API endpoint accessible privately through a VPC endpoint

Direct AWS WAF integration with API Gateway

## HTTP API

A strong default for modern applications when its feature set meets your requirements.

## 10. SAA-C03 exam scenarios

Try to remember these distinctions:

- Lower cost + simple API + JWT authorization: HTTP API.
- API keys + usage plans: REST API.
- Advanced request/response transformation: REST API.
- Built-in API Gateway caching: REST API.
- Private API accessible through an interface VPC endpoint: Private REST API.
- Direct AWS WAF integration: REST API.
- Lambda or HTTP backend integration: Both can be suitable; check the exact requirements.
- Real-time, bidirectional client-server communication: WebSocket API, not REST API or HTTP API.

The most important rule is to choose the least complex and least expensive API type that meets all the stated requirements. Don't automatically choose REST API simply because the application uses REST-style endpoints.
