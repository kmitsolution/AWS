For **AWS Certified Solutions Architect Associate (SAA-C03)**, you should understand these services from an **architectural perspective**, not necessarily in deep implementation detail.

---

# 1. Amazon Athena

![Image](https://images.openai.com/static-rsc-4/4nsIioGhdeoYbI7M7dXE6v-gKCrvOeSHDiH3avc2TWLEm3xVDtVW1BysNqkNR4QlYPLghz1tGcF7PNIlEPWtXiAacUPPwlqVAVVVnVtw0kjg9UQt4D8GjQwVpJwmx9nqDMIGOCcQlJQQoOD3a7TWsp_lvvOrmWVtipxArHkqhWj82bqUSNtD2lUd_Pm1KhNG?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/kCsjcPL5JSM_PZKN7hnrE15CUXHM9XpSltBWDOJaN72tlnw5Qo2mEHscCadrsVzE4zbLsDu5TL4oOhy1y2oBtdoUiYbkMuUu5Cu1IT2E6qIHLYTdyw8KKQyGE8dX1CLnv-Usnbp3jboBbEkzIzIGZYmhZnCcNDR1UxXERhFfvlLA8SjP0UcqsrhxEniN3xnz?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/V_7WlXkA8X1luniL2PJI_e7dC6Oson_lJwvdQR3IgjDaVq7KSKxP1ZFFlUsek3TT7HWESGZyEq2-lCHNz1bR39k86hfD4gBnuYu6lJYOhSK6kn64vYBhnlB_0hTO4gZCLI5dKDIC5-JDpzohgoH1JwJfGfPN3gv9w4oEbMzdFrmpnn7aJZC4quret2lFrbsX?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/7stdYtp5hxd6bTH_yVUzwC3eedAqc9TmoKJP6k53K7E1e_gxZZKhklXjgGCkvs6_2BZSssefkWdPUajcGiim6rVyGL5-eTZYgLrJfbVX5lV7hrVycqA5zb9zrVYW5KcmE8RlFfp6aboN5D5A59kKczkXTB26luLOAoy-uYEyMasWLPolkTQV_7kGqx70s-F8?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/g-NpOrAh5etifs6lcGGCDUl0ILzUWquO6irYPUhr8RU1978I48xEt1ESKMQkxeV4T3DL0-xfM1DC0n3LQT_6xPfZ3Wnlv3H6vzyZ6WPKiDom1bsVbp7rzbKxMQpI7yLfA8Z79J5yaOG7K1k8fvsAFSyxURK6aR7XTaVKK2fqy2QFXRgTJ10QMUtZ0tdptVvD?purpose=fullsize)

## What is Athena?

Amazon Athena is a **serverless interactive query service** that allows you to run **SQL queries directly on data stored in Amazon S3**.

It uses **Presto/Trino engine** underneath.

### Key Features

* No servers to manage.
* Pay only for the amount of data scanned.
* Supports CSV, JSON, Parquet, ORC, Avro, etc.
* Integrated with AWS Glue Data Catalog.

---

## Architecture

```text
S3 Bucket → Athena → SQL Query → Results
```

Example:

```sql
SELECT * FROM logs
WHERE status='500';
```

Athena directly reads files from S3.

---

## Use Cases

### Log Analysis

* ELB Logs
* CloudTrail Logs
* VPC Flow Logs

### Business Analytics

* Query sales data stored in S3.

### Ad-hoc Queries

* Analyze data without creating a database server.

---

## Exam Points

### Athena vs Redshift

| Feature     | Athena             | Redshift             |
| ----------- | ------------------ | -------------------- |
| Serverless  | Yes                | No                   |
| Data Stored | S3                 | Inside cluster       |
| Best For    | Occasional queries | Large data warehouse |
| Pricing     | Per TB scanned     | Per node/hour        |

---

### Cost Optimization

Store files in:

* **Parquet**
* **ORC**

because they are compressed and columnar.

This reduces scanning costs.

---

# 2. Amazon Kinesis

![Image](https://images.openai.com/static-rsc-4/VHWRfJolHDZVX-8HTeYcOidgAwOWBaRfuyMn2knKZjhawOgt9k2Zwv9muEUO5fOq6A7VTt4NBNV1Mz8d4FU_kt2r6XVseWZ5BOaAvWjnakxsWaDxmkbi8nlj94O3VAA0T2dXh260qIOXH0i1wMR85uZ_irWTArS8wbTpSd6qV_SdWRt4qPWRyL7dkCUe4iXY?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/qh-Y7MTbYQPDOo6PdabtgjRm4CA24miJwv4OdvHHIAFUmz4vvp9fc6BTyBKXjNodeLSfzMsciWhxcq9ySS9JcVqSUi7DM5ZGFB0wKbRg9I_fgPSOYjweIW_60uOan9ZomHMbL--zLcRa80N9YT1Gi1tkb6dTQP6z3uN3_oQawOITCxGjHHO8C9Jp90--cOwq?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/kE_hbKKndp4GEM8f_xTCcpeeQNQhMsOQ9r9oK7YTHBZB6SJJ_bWKRNlQYv9Gw-yTLwtR8D--WYoYHH8wVdgSAurM7U0Q1hjI2WCwZhYQs4TCDk6Z2qIoBkrziFvYrp9LuVR_oylSioD0IbCT5JregXirpXhw9FfhxKudoldH0cMwWXdMn8QvK-Gk2U9iIDd7?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/NSLAV8FKNCAqP26E0euwkSShTPcyZUo0Ot_SDYqVtcc4RvHV7JL6BrEhGjBchnSVjalKAmpBj-PboOWyU1uINja0v3HfawgUaUQCpPVj4JKoWnM4egrTnBrSsHDvuC5E3DkiJgxZxPlLG1q5r_YKolqZzYvkTQtlw5Yon9zayILltJZr8c-k5KVgSffx_LH4?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/1uETNAST6LabO3Abe4OUQPH_UoYXmWepMguahi-UIPoTNXLkksHlCdcKIonnIYVCjUknbYenk3sr-DxWz0Y435AFRPwuNbT8nSTVVPEM33CZiwNHPl55Fiz5GEQosRyVSJT58E8nVoSblb3sJNE0YU5Pgy05GAdGuyB2MqE6r5BR-AFWft2nr6jQE_W0x60O?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/A8IcWj2EMBim2hNAW0fMyI996U15NXRSjbi7oTtUJi04URDAPjHe3hUY7nHpRI19-lCwmsqOz4AXzhHTml36ZlrV03yHSSEeUtrnfBgz3DxBA-w2tdelEgBCSAAZS7ClCtGd-B-MLxffk62DXfwwaXaiBPuXLowvbFXNjTPplt9H3T5M-XPyI7NL_AhxoZfj?purpose=fullsize)

## What is Kinesis?

Amazon Kinesis is a service for **real-time streaming data ingestion and processing**.

Examples:

* IoT Sensors
* Website Clickstream
* Application Logs
* Stock Market Data

---

# Kinesis Components

---

## A. Kinesis Data Streams (KDS)

Real-time streaming platform.

```text
Producer → Kinesis Stream → Consumer
```

Examples:

* Applications
* Lambda
* EC2
* Firehose

### Important Terms

### Shard

A shard is the unit of throughput.

Per shard:

* Write = 1 MB/sec
* Read = 2 MB/sec

---

### Retention

Default:

```text
24 Hours
```

Can increase up to:

```text
365 Days
```

---

## B. Kinesis Data Firehose

```text
Producer → Firehose → S3/Redshift/OpenSearch
```

Fully managed delivery service.

Features:

* Auto scaling
* Buffering
* Compression
* Data transformation via Lambda

---

### Firehose vs Data Streams

| Feature                   | Data Streams | Firehose |
| ------------------------- | ------------ | -------- |
| Real-time custom apps     | Yes          | No       |
| Auto scaling              | No           | Yes      |
| Store in S3 automatically | No           | Yes      |
| Manage shards             | Yes          | No       |

---

## C. Kinesis Data Analytics

Allows SQL processing of streaming data.

Example:

```sql
SELECT COUNT(*)
FROM STREAM
GROUP BY TUMBLING WINDOW
```

---

## D. Kinesis Video Streams

Stores video streams from:

* CCTV
* Cameras
* IoT devices

---

## Exam Questions

### Scenario 1

You need to continuously collect website clickstream and process with Lambda.

Answer:

✅ Kinesis Data Streams

---

### Scenario 2

You simply need to load streaming data into S3.

Answer:

✅ Kinesis Firehose

---

### Scenario 3

Need near real-time analytics on stream data.

Answer:

✅ Kinesis Data Analytics

---

# 3. AWS CloudTrail

![Image](https://images.openai.com/static-rsc-4/sQJnJctPBfxSWTPQb9DnKVGS4qCL2QtI5DuqulCrFNb6EJbXWpIu3IlJh4Fy0Ppj9ZpNhT5dmV4BPl-f-orr-v1CDme54PWikuwePLmdyHq-XaWg1kOWHpJaE8_w2MNSkVH0MUBpLiySqeB2jQo5Q-QnpAckmG3mnkDKTHxlrVbFTV4_66PsaRXhozw5b7Fw?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/cQ7HNbGsq8B8dgU-EiKiLYsEmzcnve_trpWib0-B_gaHpM7k_iQQLwmHhBWHQaJ4wGZnOYv1ZRiI1lc3vqRfrEPfTZmOCXccm4N_iObGTTtPeVpsG_b8Pbg4mwwo-8jil3PQxCJjm-Xh4sEHyLmiP2vK4vKPjfO2OsbRhziRbMw4Ogmsck9yEeNwfNwC9_lA?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/8VQz4lzgMoo24YzZIp7xj1zZppEID1Moji6GunkO8krOhP3nQq_PDGlWsEymOQJpwfacn6GJK3pzddZf1BddW6lfWIt6CB0Hp1TLj2PmUlLaUZ7xaSicJ0Mr7-IFuGuk1wq8lmo6EgP_OLG4v3G-ZTL9tmL6yQQnmTrhV-DT8xV7ctmuVyBXldYG9or7DuqG?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/fa2j2H7-vulFnvWgbY35Hk4rB7KmckW43VjmWMKSrScykoC99iXGBMXVk96wv7gaoRBXRUHRJG9J6i6BpqufZhBD_njIsuPr8_vwqMF-IvgRRUOfB8GeLCEFrFn_Y92da5rkPBAP6EK1YsWMhzzLGh_nQN0e-rHwAH_AZffZ4ab1q8KgQoAkE1ymYiumE52D?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/7wpdmCl1UzCOiigco7s8fA1d0lQtoK0T6DRuZMqop7e7hfhOyDbK4LkR4ppZg8TIqOYX-bqsvJqY6X3Z4_qAV3SkAAPW5Z4ocQZAmMhTmBJU4wH0BjU6sJl-9I9vYY0wn6rLvIn8eFmFy1uV5RUQXhraJIxLpPDqkXDoN4JpvlOURrMVrteSEsFkFg04QKd2?purpose=fullsize)

## What is CloudTrail?

CloudTrail records **all API activities and account actions** in AWS.

It answers questions like:

* Who created an EC2 instance?
* Who deleted an S3 bucket?
* Which IAM user modified security groups?

---

## Example Event

```json
RunInstances
DeleteBucket
CreateUser
TerminateInstances
```

CloudTrail logs all these API calls.

---

## Architecture

```text
AWS Services
      ↓
CloudTrail
      ↓
S3 Bucket
```

Can also send logs to:

* CloudWatch Logs
* EventBridge

---

## Types of Events

### 1. Management Events

Control plane operations.

Examples:

* Create EC2
* Create IAM User
* Modify Security Groups

These are enabled by default.

---

### 2. Data Events

Object-level activities.

Examples:

* S3 GetObject
* PutObject
* Lambda Invoke

These are **not enabled by default**.

---

### 3. Insights Events

Detect unusual activities.

Examples:

* Sudden increase in:

  * `TerminateInstances`
  * `CreateUser`

---

## CloudTrail vs CloudWatch

| Feature                | CloudTrail | CloudWatch |
| ---------------------- | ---------- | ---------- |
| API Auditing           | Yes        | No         |
| Metrics                | No         | Yes        |
| Monitoring             | Limited    | Yes        |
| Logs from OS/App       | No         | Yes        |
| Security Investigation | Yes        | Limited    |

---

## Common Exam Scenarios

### Scenario 1

Need to know who deleted an S3 bucket.

✅ CloudTrail

---

### Scenario 2

Need CPU monitoring.

✅ CloudWatch

---

### Scenario 3

Need alert when someone creates IAM users.

✅ CloudTrail + EventBridge

---

# Important SAA-C03 Memory Tips

### Athena

```text
SQL on S3
Serverless
Pay per TB scanned
```

### Kinesis

```text
Real-time streaming
Data Streams → custom processing
Firehose → delivery to S3/Redshift
```

### CloudTrail

```text
Who did what in AWS?
API auditing service
Stores logs in S3
```

---

## Quick Comparison

| Service    | Purpose                           |
| ---------- | --------------------------------- |
| Athena     | Query data in S3 using SQL        |
| Kinesis    | Stream and process real-time data |
| CloudTrail | Audit AWS API activities          |
| CloudWatch | Monitoring and metrics            |
| Glue       | ETL and Data Catalog              |
| Redshift   | Data warehouse                    |

For **SAA-C03**, remember these typical keywords:

* **"Query S3 using SQL" → Athena**
* **"Streaming / clickstream / IoT" → Kinesis**
* **"Who deleted this resource?" → CloudTrail**
* **"Monitoring CPU or memory" → CloudWatch**
