## What is an ENI (Elastic Network Interface)?

An **ENI (Elastic Network Interface)** is a **virtual network card (NIC)** in AWS that is attached to resources inside a VPC.

Think of it as the network adapter of a server.

An ENI contains:

* Private IP addresses
* Elastic IP (optional)
* MAC address
* Security Groups
* Source/Destination check setting
* DNS hostname information

### Example

```
EC2 Instance
    |
    +-- ENI (eth0)
          |
          +-- Private IP: 10.0.1.10
          +-- Public IP: 54.x.x.x
          +-- Security Group: Web-SG
          +-- MAC Address
```

---

## Are Public and Private IPs Attached to ENI?

### Private IP

✅ Yes

Every ENI must have:

* One Primary Private IP
* Optional Secondary Private IPs

Example:

```
ENI-1
  Primary IP   : 10.0.1.10
  Secondary IP : 10.0.1.11
  Secondary IP : 10.0.1.12
```

---

### Public IP

Technically, the public IP is associated with the ENI's private IP.

Example:

```
Private IP : 10.0.1.10
Public IP  : 54.25.10.15
```

AWS performs NAT mapping:

```
54.25.10.15  <---->  10.0.1.10
```

So when people say "public IP attached to EC2", in reality it is associated with the ENI's private IP.

---

### Elastic IP (Static Public IP)

An Elastic IP is explicitly associated with an ENI or a private IP on an ENI.

Example:

```
ENI
  Private IP : 10.0.1.10
  Elastic IP : 3.110.10.20
```

If you move the ENI, the Elastic IP moves with it.

---

## Are Security Groups Attached to ENI?

✅ Yes

Security Groups are actually attached to the ENI, not directly to the EC2 instance.

Example:

```
EC2
 |
 +-- ENI
      |
      +-- Web-SG
      +-- SSH-SG
```

Multiple Security Groups can be attached to the same ENI.

---

## Can One EC2 Have Multiple ENIs?

✅ Yes

Example:

```
EC2 Instance
 |
 +-- ENI-1 (eth0)
 |
 +-- ENI-2 (eth1)
 |
 +-- ENI-3 (eth2)
```

The number depends on the EC2 instance type.

---

## Why Would We Need Multiple ENIs?

### 1. Separate Network Traffic

Example:

```
eth0 → Application Traffic
eth1 → Database Traffic
```

This isolates traffic flows.

---

### 2. Multiple Security Policies

Example:

```
ENI-1
  Security Group: Internet Access

ENI-2
  Security Group: Database Access
```

Different interfaces can have different Security Groups.

---

### 3. High Availability / Failover

Suppose:

```
Server-A
  |
  +-- ENI with IP 10.0.1.100
```

If Server-A fails:

```
Detach ENI
Attach ENI to Server-B
```

The private IP and security groups move immediately.

This is a common failover pattern.

---

### 4. Dual-Homed Architecture

Example:

```
            Internet
                |
             Public Subnet
                |
             ENI-1
                |
             EC2
                |
             ENI-2
                |
           Private Subnet
```

The instance acts as a firewall, proxy, or NAT device.

---

### 5. Network Appliances

Used for:

* Firewalls
* IDS/IPS
* Proxy servers
* Load balancing appliances

They often require multiple network interfaces.

---

## Can ENI Be Moved Between Instances?

✅ Yes

Example:

```
Detach from EC2-A
Attach to EC2-B
```

The following move together:

* Private IPs
* Elastic IPs
* Security Groups
* MAC Address

This makes failover very fast.

---

## Is ENI Only for EC2?

❌ No

Many AWS services use ENIs behind the scenes.

### EC2

Most common usage.

---

### AWS Lambda (VPC-enabled)

When Lambda accesses a VPC:

```
Lambda
   |
   +-- ENI
```

AWS creates ENIs in your subnets.

---

### Amazon RDS

Database instances use ENIs.

```
RDS
 |
 +-- ENI
```

---

### Amazon ECS

Tasks can receive their own ENIs using **awsvpc** networking mode.

```
ECS Task
   |
   +-- ENI
```

Each task gets its own IP.

---

### Amazon EKS

Pods can receive IPs through ENIs using the Amazon VPC CNI plugin.

```
Worker Node
   |
   +-- ENI
          |
          +-- Pod IPs
```

---

### Network Load Balancer (NLB)

AWS creates ENIs in subnets for the load balancer.

---

### AWS PrivateLink

Interface Endpoints are ENIs.

Example:

```
VPC Endpoint
     |
     +-- ENI
```

You access AWS services privately through these ENIs.

---

## Real-World Example

Suppose you have a payment application:

```
EC2 Payment Server

ENI-1 (Public)
  IP: 10.0.1.10
  SG: Web-SG
  Elastic IP attached

ENI-2 (Private)
  IP: 10.0.2.10
  SG: Database-SG
```

Traffic flow:

```
Internet Users
      |
      v
ENI-1
      |
Payment Server
      |
ENI-2
      |
RDS Database
```

The web traffic and database traffic remain completely separated.

---

