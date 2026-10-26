# AWS API Gateway Hands-on Lab: Create a User API Using HTTP API

In this lab, you will build a working serverless API using Amazon API Gateway, AWS Lambda, and Amazon DynamoDB. You will test the API using Postman.

The goal is to understand how an HTTP request travels from a client to a database and how the response returns to the client.

## 1. What will you build?

Postman / Web Application

Sends HTTP requests

Amazon API Gateway — HTTP API

Routes requests to Lambda

AWS Lambda — Python

Executes the API logic

Amazon DynamoDB

Stores user records

You will create three API operations:

| Operation     | Method | Endpoint         |
| ------------- | ------ | ---------------- |
| Create a user | POST   | `/user`          |
| Get a user    | GET    | `/user/{userId}` |
| Delete a user | DELETE | `/user/{userId}` |

This first lab uses an HTTP API. Once it works, you can build the same application using a REST API and compare their features.

## 2. AWS services required

[AWS DynamoDB Logo PNG, AI, EPS, CDR, PDF, SVG - iconLogoVector](https://images.openai.com/static-rsc-4/AJA6V0IEKtaRUdnfn2E1T4z1x3xbL0P_a5bRuYyljNZV5KMNbKW90sDqHnADoz6KOXdp9z8g6iCn6ar9czSenc4pUKPcCn1twB5nPoI4fBvvOSTqM8IX_UEfHDAXh2G7OFOG51BnaQQFOrJ2Dag695e6q45TzRy9l4NRfDQjrIk?purpose=inline)

Amazon DynamoDB

Database

[File:Amazon Lambda architecture logo.svg - Wikimedia Commons](https://images.openai.com/static-rsc-4/BVOKTuzq-WE23sUpbEYalv9OTNIt2HQXfPQ8L1sXZjnUuDWtKPluZQI3BkSmEgRPi8aSelgujA_oD_KLtmOAqPNc7ACR1HWwYDl9RjAmgG_tzIb3WTa63v82AoORkHpkPFdAQosynUb76ILUymiy2X-6gtS_Pxs0XupFcvL21GE?purpose=inline)

AWS Lambda

Python backend

[【API Gateway】Cognitoオーソライザーを使ったM2M認証でAPIを保護する｜エンジニアブログ｜パーソル＆サーバーワークス株式会社](https://images.openai.com/static-rsc-4/GofV9clQPx1xvvwzH6yiB8qnsTlkLrYk-zIvBOkWAoaKozZU8ZDRk54B3peKfnh9YB30FqKBxkbAlU3QauNF1Rcp4dRtCgUELaSZwQWq0FGrW6TxO8GxdwO6Ggt59Xyd14uD5AkbFl-usmQz88gQUDUmIdQCacRlDB9TkHTrCdk?purpose=inline)

API Gateway

HTTP endpoints

[AWS Identity and Access Management (IAM) Deep Dive | by Abhishek_Jirage | Medium](https://images.openai.com/static-rsc-4/EymdTUGqGkGDSgyy7arH04pm4hfk_xPUOAVLbS9tyc--5wv2NFRMxViUbGx9fJNG8sIVlZ6xxDyOND0KIR3uOs3-RJkamN9FoNK6PVehZ3c3uP13zyyv9rFSaPXtYyyIURtRD0pWp7CyTYc4V3FzqCe4h04LvpusNgObjagaqcc?purpose=inline)

AWS IAM

Permissions

You will also need Postman or another HTTP client to test your API.

## 3. Step 1 — Create a DynamoDB table

[Step 1: Create a table in DynamoDB - Amazon DynamoDB](https://images.openai.com/static-rsc-4/M4W7HPVexQvRo2L5cXjhZ7xC8IcSM830VtViHdkVKH2aUS1jar3B-e-TQhDCgUiW8DcNv_rIQOvGpENfjxjHhOALb0QZucw3vLUr76ebESIk9ddv-d11XCGUDOkZWk7eJyzJW1v4mF4_Jivbm8IoqcYf9YjmlsId7unkO5N5KSs?purpose=inline)

[docs.aws.amazon.com](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/getting-started-step-1.html?utm_source=chatgpt.com)

1. Sign in to the AWS Management Console.
2. Search for DynamoDB.
3. Open Tables → Create table.
4. Enter the following values.

| Setting       | Value        |
| ------------- | ------------ |
| Table name    | `UsersTable` |
| Partition key | `userId`     |
| Key type      | String       |
| Sort key      | None         |
| Capacity mode | On-demand    |

5. Keep the other settings at their defaults for this introductory lab.
6. Click Create table and wait until the table becomes active.

Your table will store records such as:

```
{
  "userId": "101",
  "name": "Kabir",
  "email": "kabir@example.com"
}
```

The `userId` is the partition key used to identify each user.

## 4. Step 2 — Create an IAM role for Lambda

Lambda needs permission to read and write items in DynamoDB.

[Lambda でデータを処理する - Amazon Monitron](https://images.openai.com/static-rsc-4/295E0K7VJb6mr9P9DlxYP0quCnUpeofMpOQJLFjffAc0k4oATg5uYpREyME0uJ72gF1NWQiXu7EhRdizayz6AqAKOnY0pL_orVYOJT9iffu1fBUmKw7j0U_CFyS-j-d-J6Izt6F7SPvAafXRXPeIjMEt-imMcbh9QAiRVnvXvcM?purpose=inline)

[docs.aws.amazon.com](https://docs.aws.amazon.com/ja_jp/Monitron/latest/user-guide/data-export-lambda-v2.html?utm_source=chatgpt.com)

1. Open IAM → Roles → Create role.
2. Choose AWS service as the trusted entity.
3. Select Lambda as the use case.
4. Add a permissions policy that allows the required DynamoDB operations on `UsersTable`.
5. Name the role `LambdaDynamoDBExecutionRole`.
6. Create the role.

For a quick disposable lab, you might see examples using `AmazonDynamoDBFullAccess`. However, that policy grants broader permissions than needed. A better approach is to create a custom policy scoped to your table ARN with only the required actions, such as `dynamodb:PutItem`, `dynamodb:GetItem`, and `dynamodb:DeleteItem`.

Also ensure that the Lambda execution role has the basic permissions needed to write logs to CloudWatch.

## 5. Step 3 — Create the Lambda functions

You will create three separate Lambda functions. Each function handles one API operation.

[Powers - Kiro](https://images.openai.com/static-rsc-4/asY-kDLjnxxl9VMjYWrphU8-fl4jfvdxuSdyLK5ZbHEsRZHv6DnHhWkGlEbkaDRnID6qkO7H1JYIulV8_9n9byGFs3gqSNBlsqX_nP40c4yMJ7MDdJJzEv1dd1Ic1UqAMwZLiHIOFRO5P6a-g8-OJ68iPKKZvx0wTWXhz6Czlyo?purpose=inline)

Function 1: `CreateUserFunction`

- Runtime: Python 3.12
- Purpose: Create a user in DynamoDB
- Database operation: `PutItem`

[Powers - Kiro](https://images.openai.com/static-rsc-4/asY-kDLjnxxl9VMjYWrphU8-fl4jfvdxuSdyLK5ZbHEsRZHv6DnHhWkGlEbkaDRnID6qkO7H1JYIulV8_9n9byGFs3gqSNBlsqX_nP40c4yMJ7MDdJJzEv1dd1Ic1UqAMwZLiHIOFRO5P6a-g8-OJ68iPKKZvx0wTWXhz6Czlyo?purpose=inline)

Function 2: `getUserFunction`

- Runtime: Python 3.12
- Purpose: Retrieve a user by ID
- Database operation: `GetItem`

[Powers - Kiro](https://images.openai.com/static-rsc-4/asY-kDLjnxxl9VMjYWrphU8-fl4jfvdxuSdyLK5ZbHEsRZHv6DnHhWkGlEbkaDRnID6qkO7H1JYIulV8_9n9byGFs3gqSNBlsqX_nP40c4yMJ7MDdJJzEv1dd1Ic1UqAMwZLiHIOFRO5P6a-g8-OJ68iPKKZvx0wTWXhz6Czlyo?purpose=inline)

Function 3: `deleteUserFunction`

- Runtime: Python 3.12
- Purpose: Delete a user by ID
- Database operation: `DeleteItem`

For each function:

1. Open Lambda → Create function.
2. Select Author from scratch.
3. Enter the function name and choose Python 3.12.
4. Under permissions, choose Use an existing role.
5. Select `LambdaDynamoDBExecutionRole`.
6. Create the function.
7. Add the corresponding Python handler code and click Deploy.

The handler must use the correct DynamoDB table name, `UsersTable`, and parse the event format expected by API Gateway.

### Test the Lambda functions before creating API Gateway

This is an important troubleshooting step. First verify that each function works independently.

For `CreateUserFunction`, use a test event like:

```
{
  "body": "{\"userId\": \"101\", \"name\": \"Kabir\", \"email\": \"kabir@example.com\"}"
}
```

Notice that `body` is a JSON string, not a nested JSON object. This resembles the event format API Gateway can send to Lambda.

For `getUserFunction`, use:

```
{
  "pathParameters": {
    "userId": "101"
  }
}
```

For `deleteUserFunction`, use:

```
{
  "pathParameters": {
    "userId": "101"
  }
}
```

Run the tests and check that the results match the intended operations. Then open DynamoDB and confirm that the user was created, retrieved, or deleted as expected.

## 6. Step 4 — Create an HTTP API in API Gateway

[Optimise and Secure AWS HTTP API Gateway by locking down direct access | SkildOps](https://images.openai.com/static-rsc-4/mwvxJ6M2Vlz1nnZJ1LFVXak10AZwmTZbHaoSG0hygEMLOMcoOoHq5qYrikFCVcNGcVHZwJz7uSwyWUGxQqrRoMnThwFDgCoXL1YAqsaV2HxsWn3goCd0M_Z0nIMlOH4sUeKK0_TSDKkw1Rc0ud8TXh92dRXuX1schBz3QHSNQ_E?purpose=inline)

[skildops.com](https://skildops.com/blog/optimise-and-secure-aws-http-api-gateway-by-locking-down-direct-access?utm_source=chatgpt.com)

1. Open Amazon API Gateway in the AWS Console.
2. Click Create API.
3. Under HTTP API, click Build.
4. Enter the API name: `UserAPI`.
5. Continue through the initial setup. You can create the routes after the API is created.
6. Keep the `$default` stage and enable automatic deployment if offered.
7. Create the API.

You now have an API Gateway resource, but you still need to define the routes and connect them to Lambda.

## 7. Step 5 — Create the API routes

A route combines an HTTP method with a path. API Gateway uses it to determine which Lambda function should receive the request.

[Amazon API Gateway with .NET - AWS Lambda & DynamoDB Integrations - codewithmukesh](https://images.openai.com/static-rsc-4/b_oYlgBPxT97uOw8CMqAc8vzYMev_SCjKNi59qDUI-4-uLSAoUmQA2waw28RzsSQpSri2eGpkgZ2RuSSHfKil9r0DCAKlexZ6cSmIx745n5ifCk8RETxblFqmwFzb43yJCEnm1MSYQZCEq46eD7HY0dFkMaWfFlpZ087k4O4Nog?purpose=inline)

[codewithmukesh.com](https://codewithmukesh.com/blog/amazon-api-gateway-with-dotnet/?utm_source=chatgpt.com)

Create the following three routes:

POST

`/user`

Create a new user

1. Open UserAPI → Routes → Create.
2. Select `POST` and enter `/user`.
3. Create the route.
4. Select the route and attach a Lambda integration.
5. Choose `CreateUserFunction` and save.

GET

`/user/{userId}`

Retrieve a user by ID

1. Create a `GET` route with path `/user/{userId}`.
2. Attach a Lambda integration.
3. Choose `getUserFunction` and save.

DELETE

`/user/{userId}`

Delete a user by ID

1. Create a `DELETE` route with path `/user/{userId}`.
2. Attach a Lambda integration.
3. Choose `deleteUserFunction` and save.

Important: The last route must use the `DELETE` method, not `GET`. Each route must be attached to the correct Lambda function.

API Gateway may ask for permission to invoke Lambda. Approve or configure the required Lambda resource-based permission so API Gateway can invoke each integration.

## 8. Step 6 — Find your API endpoint

Open your API in API Gateway and select Stages → $default. Copy the Invoke URL.

It will look similar to:

```
https://abc123xyz.execute-api.ap-south-1.amazonaws.com
```

This is only an example. Use the actual URL shown in your AWS account.

If you enabled automatic deployment for the `$default` stage, route changes are normally deployed automatically. Otherwise, deploy the API to the appropriate stage.

## 9. Step 7 — Test the API using Postman

[Getting the payload - Help Hub - Postman Community](https://images.openai.com/static-rsc-4/ty8fw6E7fTSvwcGJMTWSwI_szukbrWnzXA89dBlAsIqNPoEMucAlfFchxVzo3t0oP0YWgZ9RiI5Eq0Te8lWVJbPxHvM56JxQ-PBjte2qdAYUkWw-RU9YNnVbJCn2jsmFZ1C-XRONHh0LF5tTTgDD6-wZk7WOhA_MVNieeHIYd1Q?purpose=inline)

[community.postman.com](https://community.postman.com/t/getting-the-payload/76853?utm_source=chatgpt.com)

Replace `YOUR_API_URL` below with the actual Invoke URL.

### Test 1: Create a user

- Method: `POST`
- URL: `YOUR_API_URL/user`
- Body → raw → JSON

```
{
  "userId": "101",
  "name": "Kabir",
  "email": "kabir@example.com"
}
```

Click Send.

Expected result: a successful response from your Lambda function, commonly HTTP `201` if your handler returns that status.

### Test 2: Get the user

- Method: `GET`
- URL: `YOUR_API_URL/user/101`

Click Send.

Expected result:

```
{
  "userId": "101",
  "name": "Kabir",
  "email": "kabir@example.com"
}
```

### Test 3: Delete the user

- Method: `DELETE`
- URL: `YOUR_API_URL/user/101`

Click Send.

Expected result: a success message from Lambda.

Finally, send the same GET request again. If your function returns `404` for missing records, you should now receive a user-not-found response.

## 10. Lab completion checklist

### Lab progress

0 of 8 completed

Create UsersTable in DynamoDB

Create IAM execution role for Lambda

Create and test CreateUserFunction

Create and test getUserFunction

Create and test deleteUserFunction

Create UserAPI using HTTP API

Configure all three routes and Lambda integrations

Test POST, GET, and DELETE using Postman

Security reminder: Unless you configure authorization, your API may be publicly callable. For this introductory lab, use sample data only, avoid exposing sensitive information, and delete the lab resources when finished to avoid unnecessary charges.

Once all three operations work, you will have built a complete introductory serverless API and will be ready to repeat the lab using API Gateway REST API.
