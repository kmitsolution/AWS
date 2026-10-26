# What is CRUD and Why Do We Use It in APIs?

CRUD is one of the fundamental concepts in application development. When you build a web application, mobile application, or cloud application, you usually need to create, read, update, and delete data.

Let's understand CRUD using an AWS example with API Gateway, AWS Lambda, and Amazon DynamoDB.

## 1. What is CRUD?

CRUD stands for four basic operations performed on application data.

C — Create

HTTP method: POST

Add a new record to the database.

Example: Register a new user.

R — Read

HTTP method: GET

Retrieve existing data.

Example: View a user's profile.

U — Update

HTTP methods: PUT or PATCH

Modify existing data.

Example: Change a user's email address.

D — Delete

HTTP method: DELETE

Remove a record.

Example: Delete a user's account.

These HTTP methods are conventional mappings to CRUD operations. The HTTP method alone does not perform the database operation; your backend code implements the required logic.

## 2. Why do we need CRUD in an application?

Imagine that you are developing a website where users can register and manage their profiles.

Your application needs to support these actions:

- Register a new user.
- View user details.
- Update the user's name or email.
- Delete a user account.

Assume the user data is stored in Amazon DynamoDB.

[AWS DynamoDB Logo PNG, AI, EPS, CDR, PDF, SVG - iconLogoVector](https://images.openai.com/static-rsc-4/AJA6V0IEKtaRUdnfn2E1T4z1x3xbL0P_a5bRuYyljNZV5KMNbKW90sDqHnADoz6KOXdp9z8g6iCn6ar9czSenc4pUKPcCn1twB5nPoI4fBvvOSTqM8IX_UEfHDAXh2G7OFOG51BnaQQFOrJ2Dag695e6q45TzRy9l4NRfDQjrIk?purpose=inline)

Amazon DynamoDB

Stores application records

| userId | name  | email              |
| ------ | ----- | ------------------ |
| 101    | Kabir | kabir\@example.com |
| 102    | Priya | priya\@example.com |

The database stores the data, but your frontend needs a safe and controlled way to interact with it.

That's where APIs and backend logic come in.

## 3. How does a website communicate with DynamoDB?

A browser should not receive privileged AWS credentials or direct database permissions. A common architecture is to put an API and backend between the frontend and the database.

Website — Angular / React

User clicks Register, View, Update, or Delete

Amazon API Gateway

API endpoints, authorization, throttling, logging

AWS Lambda

Validates requests and implements CRUD logic

Amazon DynamoDB

Stores and retrieves user records

Responses travel back through Lambda and API Gateway to the website.

### What does each service do?

- API Gateway: Exposes HTTPS endpoints and controls how clients access them.
- Lambda: Executes application logic, checks inputs, and calls DynamoDB.
- DynamoDB: Stores the user records.
- IAM: Gives Lambda the permissions needed to access the appropriate DynamoDB table.

API Gateway does not automatically create your CRUD logic. You configure routes and integrations, and implement the operations in Lambda or another backend.

## 4. Implementing each CRUD operation

Let's use a `Users` table with `userId` as its partition key.

### A. Create — POST

Suppose a user registers on your website.

Request:

```
POST /users
Content-Type: application/json
```

```
{
  "userId": "101",
  "name": "Kabir",
  "email": "kabir@example.com"
}
```

The flow is:

1. The website sends the request.
2. API Gateway routes it to Lambda.
3. Lambda validates the submitted data.
4. Lambda writes the record to DynamoDB.
5. The API returns a success response.

For DynamoDB, you might use `PutItem`. If you must prevent an existing user from being overwritten, use a suitable condition expression or another safe creation strategy.

Example response

```
{
  "message": "User created successfully",
  "userId": "101"
}
```

### B. Read — GET

When the user opens their profile:

```
GET /users/101
```

Lambda can use DynamoDB `GetItem` to retrieve a user by its primary key.

The flow is:

```
GET /users/101
      ↓
API Gateway
      ↓
Lambda
      ↓
DynamoDB GetItem
      ↓
User record returned
```

Example response:

```
{
  "userId": "101",
  "name": "Kabir",
  "email": "kabir@example.com"
}
```

For retrieving a single record by its key, `GetItem` is generally appropriate. For retrieving multiple records, consider `Query` where the table's key design supports it. `Scan` examines table items and is often less efficient for targeted lookups.

### C. Update — PUT or PATCH

Suppose Kabir changes his email address.

A PUT request might be:

```
PUT /users/101
```

```
{
  "name": "Kabir",
  "email": "kabir.new@example.com"
}
```

A PATCH request could instead send only the field that changed:

```
PATCH /users/101
```

```
{
  "email": "kabir.new@example.com"
}
```

Lambda can use DynamoDB `UpdateItem` to update the record.

- PUT conventionally replaces the resource representation.
- PATCH conventionally modifies selected fields.

Your backend must implement the intended semantics. DynamoDB's `UpdateItem` can update selected attributes, but it does not automatically make a PUT operation behave like a full resource replacement.

### D. Delete — DELETE

When a user requests account deletion:

```
DELETE /users/101
```

Lambda can call DynamoDB `DeleteItem` using `userId` as the key.

After deletion, the API may return:

```
{
  "message": "User deleted successfully"
}
```

In a production application, authenticate the caller and verify they are authorized to delete that particular account before performing the operation.

## 5. CRUD API endpoint summary

| Operation     | HTTP method  | Example endpoint | DynamoDB operation |
| ------------- | ------------ | ---------------- | ------------------ |
| Create        | POST         | `/users`         | `PutItem`          |
| Read one user | GET          | `/users/101`     | `GetItem`          |
| Update        | PUT or PATCH | `/users/101`     | `UpdateItem`       |
| Delete        | DELETE       | `/users/101`     | `DeleteItem`       |

These are typical mappings, not mandatory one-to-one rules. The backend determines the actual database operation.

## 6. Interactive example: Which CRUD operation is being performed?

A user changes their email address from kabir\@example.com to kabir.new\@example.com. Which operation is this?

Create (POST)

Read (GET)

Update (PUT/PATCH)

Delete (DELETE)

Check answer

## 7. Why do we use API Gateway?

API Gateway provides a managed entry point for clients to access backend functionality.

It can provide:

- Security: Integrate authentication and authorization.
- Throttling: Help control incoming request rates.
- Logging and monitoring: Track API requests and errors.
- Routing: Direct requests to the appropriate backend.
- Custom domains: Expose APIs through a friendly URL.

Remember that API Gateway is not the database and does not replace Lambda's application logic.

## 8. Complete example to remember

Frontend

User performs a CRUD action

API Gateway

Receives the HTTP request

Lambda

Executes the CRUD business logic

DynamoDB

Performs the required data operation

For AWS SAA-C03, remember: CRUD describes the four data operations; HTTP methods communicate the requested action; Lambda implements the backend logic; DynamoDB stores the data; and API Gateway exposes the API to clients.
