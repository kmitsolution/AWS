# AWS EC2 Instance Purchasing Options

## 1. Introduction

Amazon EC2 provides different purchasing options that allow organizations to optimize their compute costs based on workload requirements, usage patterns, and commitment duration.

AWS provides the following purchasing options:

1. On-Demand Instances
2. Spot Instances
3. Reserved Instances (Standard)
4. Reserved Instances (Convertible)
5. Scheduled Reserved Instances
6. Savings Plans (EC2 Instance Savings Plans)
7. Savings Plans (Compute Savings Plans)

---

## 2. EC2 Purchasing Options

### 2.1 On-Demand Instances

On-Demand Instances allow you to pay for compute capacity by the time you use it, without making any long-term commitment.

**Key Features:**

- Pay-as-you-go pricing model.
- No upfront payment.
- No long-term commitment.
- Suitable for unpredictable workloads.
- You can start or stop instances whenever required.

**Example:**

Suppose you launch an EC2 instance for testing purposes and use it only for 5 hours.

You pay only for the usage duration according to the applicable pricing model.

**Use Cases:**

- Development environments.
- Testing environments.
- Short-term workloads.
- Applications with unpredictable traffic.

### 2.2 Spot Instances

Spot Instances allow you to purchase unused AWS EC2 capacity at significantly discounted prices.

AWS can interrupt Spot Instances when it needs the capacity back.

**Key Features:**

- Bidding-based purchasing model.
- No long-term commitment.
- Potential savings of up to 90% compared to On-Demand pricing.
- Instances can be interrupted by AWS.
- Suitable for fault-tolerant workloads.

**Example:**

Suppose you are running a batch-processing application that processes large amounts of data.

If an instance gets interrupted, the application can restart the task on another available instance.

**Use Cases:**

- Batch processing.
- Big data processing.
- CI/CD workloads.
- Data analysis.
- Stateless applications.

**Important:** Do not use Spot Instances for workloads that cannot tolerate interruptions.

### 2.3 Reserved Instances (Standard)

Reserved Instances allow you to reserve capacity usage for a specific instance configuration and receive discounted pricing.

**Key Features:**

- Commitment of 1 or 3 years.
- No upfront, partial upfront, and all upfront payment options.
- Potential savings of up to 72%.
- Supports changing instance size within the same instance family.
- Does not support changing instance family under the comparison shown in the reference table.

**Example:**

Suppose your application continuously runs on a `t2.small` EC2 instance.

Instead of paying On-Demand prices, you purchase a 1-year Reserved Instance to reduce costs.

**Use Cases:**

- Production workloads.
- Applications with predictable usage.
- Long-running EC2 instances.

### 2.4 Reserved Instances (Convertible)

Convertible Reserved Instances provide more flexibility than Standard Reserved Instances.

They allow you to exchange your reservation for another configuration when your infrastructure requirements change, subject to AWS exchange rules.

**Key Features:**

- Commitment of 1 or 3 years.
- No upfront, partial upfront, and all upfront payment options.
- Potential savings of up to 72% (as shown in the reference comparison).
- Supports changing instance size.
- Supports changing instance family.
- Supports changing operating system.

**Example:**

Initially, your application runs on a `t2.small` instance.

Later, your application requires more compute power, and you want to move to a `c5` instance family.

Convertible Reserved Instances provide flexibility to make such changes through an eligible exchange.

**Use Cases:**

- Production workloads.
- Infrastructure with changing requirements.
- Applications requiring long-term cost optimization.

### 2.5 Scheduled Reserved Instances

Scheduled Reserved Instances allow you to reserve EC2 capacity for recurring time windows.

**Key Features:**

- One-year commitment.
- No upfront, partial upfront, and all upfront payment options.
- Potential savings of up to 70% (as shown in the reference comparison).
- Suitable for workloads that run on predictable schedules.

**Example:**

Suppose an organization runs a reporting application every day between 9 AM and 6 PM.

Scheduled capacity can help optimize the cost of such predictable usage.

**Use Cases:**

- Scheduled batch processing.
- Periodic reporting applications.
- Recurring business workloads.

### 2.6 Savings Plans (EC2 Instance Savings Plans)

EC2 Instance Savings Plans provide discounted pricing in exchange for committing to a consistent amount of compute usage measured in dollars per hour.

They offer flexibility within a specific EC2 instance family and AWS Region.

**Key Features:**

- Commitment of 1 or 3 years.
- No upfront, partial upfront, and all upfront payment options.
- Potential savings of up to 72% (as shown in the reference comparison).
- Supports changing instance size.
- Supports changing operating system.
- Does not provide the same instance-family and Region flexibility as Compute Savings Plans.

**Example:**

Suppose you commit to consistent EC2 usage within a particular instance family and Region.

AWS applies the Savings Plans discount to eligible usage.

### 2.7 Savings Plans (Compute Savings Plans)

Compute Savings Plans provide greater flexibility across different compute services.

**Key Features:**

- Commitment of 1 or 3 years.
- No upfront, partial upfront, and all upfront payment options.
- Potential savings of up to 66% (as shown in the reference comparison).
- Supports changing instance size.
- Supports changing instance family.
- Supports changing operating system.
- Supports changing AWS Region.
- Provides flexibility across eligible compute services, including EC2, AWS Lambda, and AWS Fargate.

**Example:**

Initially, your application runs on EC2.

Later, you migrate part of your workload to AWS Lambda.

Compute Savings Plans provide flexibility to apply eligible discounts across supported compute services.

---

## 3. Comparison of EC2 Purchasing Options

| Feature                | On-Demand     | Spot      | Reserved Standard | Reserved Convertible | Scheduled RI | EC2 Savings Plan | Compute Savings Plan |
| ---------------------- | ------------- | --------- | ----------------- | -------------------- | ------------ | ---------------- | -------------------- |
| Pricing Model          | Pay-as-you-go | Bidding   | Commitment        | Commitment           | Commitment   | Commitment       | Commitment           |
| Commitment             | None          | None      | 1 or 3 years      | 1 or 3 years         | 1 year       | 1 or 3 years     | 1 or 3 years         |
| Cost Savings\*         | None          | Up to 90% | Up to 72%         | Up to 72%            | Up to 70%    | Up to 72%        | Up to 66%            |
| Instance Interruption  | No            | Yes       | No                | No                   | No           | No               | No                   |
| Change Size            | Yes           | Yes       | Yes               | Yes                  | Yes          | Yes              | Yes                  |
| Change Family          | Yes           | Yes       | No                | Yes                  | No           | No               | Yes                  |
| Change Compute Service | No            | No        | No                | No                   | No           | No               | Yes                  |
| Change OS              | Yes           | Yes       | No                | Yes                  | No           | Yes              | Yes                  |
| Change Region          | Yes           | Yes       | No                | No                   | No           | No               | Yes                  |

\*Savings percentages are reproduced from the supplied comparison chart and are illustrative, not guaranteed discounts.

---

## 4. Which EC2 Purchasing Option Should You Choose?

| Requirement                                              | Recommended Option             |
| -------------------------------------------------------- | ------------------------------ |
| Short-term development and testing                       | On-Demand                      |
| Fault-tolerant batch processing                          | Spot                           |
| Predictable long-running workloads                       | Standard Reserved Instances    |
| Long-term workloads requiring configuration flexibility  | Convertible Reserved Instances |
| Workloads running on fixed schedules                     | Scheduled Reserved Instances   |
| Consistent EC2 usage within a specific family and Region | EC2 Instance Savings Plans     |
| Flexible compute usage across services and Regions       | Compute Savings Plans          |

---

# 5. EC2 Instance Store vs Amazon EBS

## Introduction

When you launch an EC2 instance, you need storage to store operating system files, application files, databases, and other data.

AWS provides two major storage options associated with EC2:

1. EC2 Instance Store
2. Amazon Elastic Block Store (EBS)

Although both provide storage for EC2 instances, they differ in persistence, lifecycle, performance, and cost.

## 5.1 EC2 Instance Store

EC2 Instance Store provides temporary block-level storage physically attached to the host computer running your EC2 instance.

It is suitable for temporary data that does not need to survive the lifecycle of the instance.

**Key Features:**

- Temporary storage.
- Data is tied to the instance lifecycle.
- Data is lost if the instance is stopped, terminated, or fails, according to the reference comparison.
- Storage size depends on the EC2 instance type.
- Provides high IOPS and throughput.
- Storage cost is included with the EC2 instance price.
- Automatically available when the instance launches, depending on the instance type and configuration.

**Use Cases:**

- Temporary cache.
- Scratch space.
- Temporary buffers.
- Temporary application data.
- Data processing workloads.

**Example:**

Suppose you are running an application that processes large files.

During processing, the application creates temporary files.

You can use Instance Store for this temporary data because it does not need to be retained permanently.

## 5.2 Amazon Elastic Block Store (EBS)

Amazon EBS is a block-level storage service that provides persistent storage volumes for EC2 instances.

EBS volumes can continue to exist independently of the lifecycle of the EC2 instance.

**Key Features:**

- Persistent storage.
- Data remains available until the EBS volume is deleted.
- Independent of the EC2 instance lifecycle.
- Supports volumes up to 16 TiB for most instance types, as indicated in the reference.
- Performance depends on the EBS volume type and configuration.
- Supports high IOPS and throughput with appropriate configurations.
- Charged separately based on provisioned storage and I/O.
- Must be attached and mounted to the EC2 instance.

**Use Cases:**

- Databases.
- File systems.
- Operating system boot volumes.
- Application data.
- Persistent storage requirements.

**Example:**

Suppose you launch an EC2 instance hosting a MySQL database.

You need to store database files permanently.

You can attach an EBS volume to the EC2 instance and store the database files on it.

Even if the EC2 instance is stopped, the EBS volume can retain the data.

---

## 6. EC2 Instance Store vs EBS Comparison

| Feature          | EC2 Instance Store                                                | Amazon EBS                                                 |
| ---------------- | ----------------------------------------------------------------- | ---------------------------------------------------------- |
| Nature           | Temporary storage                                                 | Persistent storage                                         |
| Lifespan         | Tied to instance lifecycle                                        | Independent of instance lifecycle                          |
| Data Persistence | Data lost if instance is stopped, terminated, or fails            | Data persists until volume is deleted                      |
| Size Limits      | Depends on instance type                                          | Up to 16 TiB for most types, as indicated in the reference |
| Performance      | High IOPS and throughput                                          | Varies by volume type and configuration                    |
| Use Cases        | Cache, scratch space, buffers                                     | Databases, file systems, boot volumes                      |
| Cost             | Included with EC2 instance                                        | Charged separately based on provisioned storage and I/O    |
| Mounting         | Automatically available on EC2 launch, depending on configuration | Must be attached and mounted to EC2 instance               |

---

## 7. Practical Examples

### Scenario 1: Temporary Application Cache

**Requirement:** An application needs temporary storage to cache frequently accessed data.

**Recommended:** EC2 Instance Store.

Reason: The data is temporary and does not need to survive instance termination.

### Scenario 2: Database Storage

**Requirement:** An EC2 instance is hosting a production database, and database files must remain persistent.

**Recommended:** Amazon EBS.

Reason: EBS provides persistent storage independent of the EC2 instance lifecycle.

### Scenario 3: Operating System Boot Volume

**Requirement:** You need a persistent root volume for an EC2 instance.

**Recommended:** Amazon EBS.

Reason: EBS supports persistent boot volumes.

---

## 8. Important Points for AWS Certification

1. On-Demand Instances do not require long-term commitments.
2. Spot Instances provide discounted unused EC2 capacity but can be interrupted.
3. Standard Reserved Instances provide long-term pricing benefits with limited flexibility.
4. Convertible Reserved Instances provide greater configuration flexibility.
5. Savings Plans require a commitment to a consistent hourly spend.
6. Compute Savings Plans provide more flexibility than EC2 Instance Savings Plans.
7. EC2 Instance Store provides temporary storage.
8. Amazon EBS provides persistent block storage.
9. EBS volumes can exist independently of EC2 instances.
10. Instance Store is useful for temporary cache and scratch data.

---

## 9. Quick Revision

| Question                                                        | Answer               |
| --------------------------------------------------------------- | -------------------- |
| Which EC2 option provides pay-as-you-go pricing?                | On-Demand            |
| Which option provides up to 90% savings in the reference chart? | Spot                 |
| Which EC2 option can be interrupted by AWS?                     | Spot                 |
| Which Reserved Instance supports changing instance family?      | Convertible          |
| Which Savings Plan provides more compute service flexibility?   | Compute Savings Plan |
| Which storage is temporary?                                     | EC2 Instance Store   |
| Which storage is persistent?                                    | EBS                  |
| Which storage is suitable for databases?                        | EBS                  |
| Which storage is useful for temporary cache?                    | Instance Store       |
| Can an EBS volume exist independently of an EC2 instance?       | Yes                  |

---

## 10. Conclusion

Selecting the right EC2 purchasing option helps organizations optimize infrastructure costs while meeting workload requirements.

Similarly, selecting the right storage option is important for application performance and data persistence.

**Remember:**

- Use On-Demand for flexibility.
- Use Spot for interruptible workloads.
- Use Reserved Instances for predictable long-term workloads.
- Use Savings Plans for committed compute usage with pricing flexibility.
- Use Instance Store for temporary storage.
- Use EBS for persistent storage.

**AWS Documentation:**

- [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/)
- [Amazon EC2 Instance Purchasing Options](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-purchasing-options.html)
- [Amazon EBS Documentation](https://docs.aws.amazon.com/ebs/latest/userguide/what-is-ebs.html)
- [EC2 Instance Store](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html)

---

*End of Document*

Suggested GitHub repository structure:

```
AWS-Cloud-Computing/
│
├── EC2/
│   ├── EC2-Instance-Purchase-Options.md
│   ├── EC2-Instance-Types.md
│   ├── EC2-Key-Pairs.md
│   ├── EC2-Security-Groups.md
│   └── EC2-Storage-EBS.md
```

This document covers both comparison tables from your original PDF (pages 1 and 2), with explanations and certification revision questions.
