# AWS CloudWatch – Introduction

## 1. What is Amazon CloudWatch?

**Amazon CloudWatch** is an observability and monitoring service provided by AWS.

It helps us to:

- Monitor AWS resources.
- Collect and analyze logs.
- Track resource performance.
- Create alarms when something goes wrong.

### Core Capabilities of CloudWatch

CloudWatch mainly provides three core capabilities:

| Capability | Purpose |
|---|---|
| Monitoring | Track the performance and health of AWS resources. |
| Alarms | Generate alerts when specific conditions are met. |
| Logging | Collect and manage application and resource logs. |

---

## 2. CloudWatch Monitoring

CloudWatch Monitoring is used to track the performance and health of AWS resources.

For example, when we launch an EC2 instance, CloudWatch helps us monitor:

- CPU Utilization
- Network Traffic
- Disk Activity

This information helps us understand whether a resource is:

1. Healthy
2. Overutilized
3. Underutilized
4. Properly sized

### 2.1 EC2 Monitoring Example

Suppose we have an EC2 instance running our application.

We want to understand:

- Is my EC2 instance overutilized?
- Is my EC2 instance underutilized?

Let's understand both scenarios.

### Case 1: EC2 Instance is Overutilized

**Example:**

Suppose CloudWatch shows the following information:

| Metric | Observation |
|---|---|
| CPU Usage | 90% |
| Network Usage | High |
| Disk Activity | High |

This indicates that our EC2 instance is under heavy load.

**Impact:**

- Application may become slow.
- User experience may be affected.
- Website may not respond properly.

**Solution:**

Increase the EC2 instance size.

Example:

`t3.small → t3.medium`

This process is called vertical scaling.

### Case 2: EC2 Instance is Underutilized

**Example:**

Suppose CloudWatch shows:

| Metric | Observation |
|---|---|
| CPU Usage | 5% |
| Network Usage | Very Low |
| Disk Activity | Very Low |

This indicates that the EC2 instance is bigger than required.

**Impact:**

- We are paying more money.
- Resources are not being properly utilized.

**Solution:**

Decrease the EC2 instance size.

Example:

`t3.medium → t3.small`

### Comparison: Overutilized vs Underutilized

| Parameter | Overutilized | Underutilized |
|---|---|---|
| CPU Usage | Very High | Very Low |
| Network Usage | High | Very Low |
| Resource Usage | Heavy Load | Low Usage |
| Impact | Application may become slow | Resources are wasted |
| Solution | Increase instance size | Decrease instance size |

---

## 3. CloudWatch Metrics

CloudWatch Metrics are performance data points collected from AWS resources.

Different AWS services publish different metrics based on their functionality.

Let's understand the metrics available for some commonly used AWS services.

### 3.1 EC2 Metrics

For EC2 instances, CloudWatch can show:

| Metric | Description |
|---|---|
| CPU Utilization | CPU usage of the EC2 instance |
| Network In | Incoming network traffic |
| Network Out | Outgoing network traffic |
| Disk Read | Disk read activity |
| Disk Write | Disk write activity |

### 3.2 AWS Lambda Metrics

For AWS Lambda, CloudWatch can show:

| Metric | Description |
|---|---|
| Invocations | Number of times a Lambda function is invoked |
| Errors | Number of failed function executions |
| Duration | Time taken by the function to execute |
| Throttles | Number of invocation requests throttled |
| Concurrent Executions | Number of function executions running simultaneously |

### 3.3 Application Load Balancer (ALB) Metrics

For Application Load Balancer, CloudWatch can show:

| Metric | Description |
|---|---|
| Request Count | Total number of requests received |
| Target Response Time | Time taken by targets to respond |
| HTTP 4XX Errors | Number of client-side HTTP errors |
| HTTP 5XX Errors | Number of server-side HTTP errors |
| Healthy Host Count | Number of healthy registered targets |
| UnHealthy Host Count | Number of unhealthy registered targets |

### 3.4 Amazon S3 Metrics

For Amazon S3, CloudWatch can show:

| Metric | Description |
|---|---|
| Bucket Size | Total size of data stored in the bucket |
| Number of Objects | Total number of objects in the bucket |
| All Requests | Total number of requests |
| Get Requests | Number of GET requests |
| Put Requests | Number of PUT requests |
| 4XX Errors | Number of client-side errors |
| 5XX Errors | Number of server-side errors |

---

## 4. Important Points to Remember

1. CloudWatch is an observability service in AWS.
2. CloudWatch provides three core capabilities:
   - Monitoring
   - Alarms
   - Logging
3. Monitoring helps us understand the performance and health of AWS resources.
4. CloudWatch Metrics are performance data points collected from AWS resources.
5. Different AWS services publish different metrics.
6. Metrics help us understand resource performance, health, and usage.
7. Monitoring helps identify overutilized and underutilized resources.

---

## 5. Real-World Scenario

Suppose we are hosting an e-commerce application on AWS.

Our architecture contains:

- EC2 instances running the application.
- Application Load Balancer distributing traffic.
- S3 storing product images.
- Lambda functions processing background tasks.

Using CloudWatch, we can monitor:

| AWS Resource | What We Monitor |
|---|---|
| EC2 | CPU, Network, Disk Activity |
| ALB | Request Count, Response Time, HTTP Errors |
| S3 | Bucket Size, Number of Objects, Requests |
| Lambda | Invocations, Errors, Duration, Throttles |

This helps the DevOps team understand application performance and identify potential issues.

---

## 6. Conclusion

Amazon CloudWatch plays an important role in AWS monitoring and observability.

It helps DevOps engineers monitor AWS infrastructure, understand resource utilization, identify performance issues, and maintain application health.

**Remember:**

Monitoring helps us understand what is happening in our infrastructure, while Alarms help us get notified when something requires attention.

---

## 7. Next Topics

- CloudWatch Metrics in Detail
- CloudWatch Dashboards
- CloudWatch Alarms
- CloudWatch Logs
- CloudWatch Agent
- Practical Monitoring of EC2 Instances

**Next Lecture:** AWS CloudWatch – Metrics and Monitoring Practical
