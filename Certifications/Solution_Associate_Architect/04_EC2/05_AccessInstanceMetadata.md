The IP address `169.254.169.254` is a special **link-local IP address** that AWS EC2 instances use to retrieve **instance metadata** and **user data**. This address is not accessible externally or from the public internet—it's specifically reserved for internal communication within an EC2 instance. When your EC2 instance sends a request to this IP address, AWS provides metadata related to the instance, such as its instance ID, AMI ID, public/private IPs, security groups, IAM role information, and more.

### **How to Use the Metadata Service (169.254.169.254) on EC2 Instances**

#### **Accessing Instance Metadata from Within the Instance:**

You can access instance metadata from any running EC2 instance by making an HTTP request to `http://169.254.169.254/latest/meta-data/` or `http://169.254.169.254/latest/user-data/`. Below are examples of how to query different types of metadata using `curl` or other tools on your EC2 instance.

#### **1. Metadata Basics**

- **URL Format**: 
  - `http://169.254.169.254/latest/meta-data/` – To get metadata information.
  - `http://169.254.169.254/latest/user-data/` – To get user data.

#### **2. Using `curl` to Access Metadata**

You can use `curl` to query various metadata details. Here are some examples:

- **Instance ID**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/instance-id
  ```

- **Public IP Address**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/public-ipv4
  ```

- **Private IP Address**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/local-ipv4
  ```

- **AMI ID**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/ami-id
  ```

- **Instance Type**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/instance-type
  ```

- **Security Groups**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/security-groups
  ```

- **Availability Zone**:
  ```bash
  curl http://169.254.169.254/latest/meta-data/placement/availability-zone
  ```

#### **3. User Data**

User data is often used to run scripts during the instance's first boot. To retrieve the user data:

- **Get User Data**:
  ```bash
  curl http://169.254.169.254/latest/user-data
  ```

User data might contain setup scripts or configurations that were passed to the instance when it was launched.

---

### **Example: Common EC2 Metadata Queries**

Let’s say you want to fetch the **instance ID**, **AMI ID**, and **public IP** of your EC2 instance using `curl`. You would run the following commands on the instance:

```bash
# Instance ID
curl http://169.254.169.254/latest/meta-data/instance-id

# AMI ID
curl http://169.254.169.254/latest/meta-data/ami-id

# Public IP
curl http://169.254.169.254/latest/meta-data/public-ipv4
```

The response to these commands would be something like:

```
i-0123456789abcdef0      (Instance ID)
ami-0abcdef1234567890    (AMI ID)
54.123.45.67              (Public IP)
```

---

### **Security Considerations:**

- **Private to EC2 Instances**: The metadata service is only available from within the instance, meaning external entities cannot access the metadata by default. This enhances security by limiting exposure.
  
- **Instance Metadata Service v2 (IMDSv2)**: AWS introduced **Instance Metadata Service v2** (IMDSv2) as an additional layer of security. IMDSv2 requires the use of a session token for accessing metadata. If your EC2 instance is configured to use IMDSv2, you'll need to make the requests using a session token.

  To check if your instance supports IMDSv2, you can run:

  ```bash
  curl -I http://169.254.169.254/latest/meta-data/
  ```

  If IMDSv2 is enabled, the response will indicate that the instance requires the session token header.

  **Using IMDSv2**:
  - First, create a session token:
    ```bash
    curl -X PUT http://169.254.169.254/latest/api/token -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"
    ```

  - Then, pass the token in subsequent requests:
    ```bash
    curl -H "X-aws-ec2-metadata-token: YOUR_SESSION_TOKEN" http://169.254.169.254/latest/meta-data/instance-id
    ```

---

### **Common Use Cases for EC2 Metadata**:

- **Automating Instance Configuration**: Use the metadata to configure instances dynamically based on instance type, security group, or region.
- **Fetching Instance Role Credentials**: If your EC2 instance is attached to an IAM role, you can use metadata to get temporary credentials for AWS services, such as S3 or DynamoDB.
- **Instance Monitoring and Logging**: Use instance metadata to gather information for monitoring tools or logging systems to track your instance’s status and configuration.

## Access with V2
The code you provided is for interacting with **Instance Metadata Service v2 (IMDSv2)**, which was introduced by AWS to enhance the security of metadata access. IMDSv2 requires a session token for metadata requests, which adds an additional layer of security compared to the original metadata service (IMDSv1). 

Let’s walk through your steps and explain each part.

---

### **Step-by-Step Breakdown**

1. **Set the Token**:
   The first step is to request a session token from the metadata service using `curl`. This is done with the following command:

   ```bash
   export TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")
   ```

   - **What it does**:
     - The `curl` request sends a PUT request to the metadata API at `http://169.254.169.254/latest/api/token`.
     - The `X-aws-ec2-metadata-token-ttl-seconds` header specifies how long the token will be valid (in this case, `21600` seconds, which equals **6 hours**).
     - The response will contain a session token, which is saved into the `TOKEN` environment variable.

2. **Echo the Token**:
   After the session token is generated, it is stored in the `TOKEN` variable. To confirm the token has been stored, you can run:

   ```bash
   echo $TOKEN
   ```

   This will display the generated token to verify it's correctly stored in the environment variable.

3. **Access Metadata with the Token**:
   Now that you have the token, you need to include it in your requests to the metadata service. The following command retrieves metadata with the token:

   ```bash
   curl -H "X-aws-ec2-metadata-token: $TOKEN" -i http://169.254.169.254/latest/
   ```

   - **What it does**:
     - The `curl` command makes a request to the metadata service at `http://169.254.169.254/latest/` (the base path for metadata).
     - The `-H` option adds the token header `X-aws-ec2-metadata-token: $TOKEN` to the request.
     - The `-i` flag tells `curl` to include the HTTP response headers in the output.
     - If successful, it will return `HTTP/1.1 200 OK` along with the metadata for the instance.

4. **Shorthand Hack for Future Requests**:
   To make working with IMDSv2 easier without needing to specify the token for every request, you can create an **alias** in your shell:

   ```bash
   alias curl='curl -H "X-aws-ec2-metadata-token: $TOKEN"'
   ```

   - **What it does**:
     - This creates an alias that automatically adds the metadata token to each `curl` request you make in the current shell session.
     - Whenever you use `curl` to access the metadata service, it will automatically include the token in the headers.

   Now, any subsequent `curl` command will use the token without you needing to explicitly pass it each time. For example:

   ```bash
   curl -i http://169.254.169.254/latest/
   ```

   This command will now work without needing to manually pass the token header every time. It will include the token automatically via the alias.

---

### **Example of Using IMDSv2 with Token**

1. **Get Instance ID**:

   ```bash
   curl -i http://169.254.169.254/latest/meta-data/instance-id
   ```

2. **Get Public IP**:

   ```bash
   curl -i http://169.254.169.254/latest/meta-data/public-ipv4
   ```

3. **Get AMI ID**:

   ```bash
   curl -i http://169.254.169.254/latest/meta-data/ami-id
   ```

---

### **Why Use IMDSv2?**

IMDSv2 enhances security by mitigating potential risks, such as:

- **Preventing SSRF (Server Side Request Forgery)**: If an attacker were to compromise an application running on your EC2 instance, they could try to query the metadata endpoint. With IMDSv2, the requirement of a session token helps reduce the risk of malicious internal applications from accessing the metadata.
  
- **Stronger Authentication**: Instead of using a token-less request (as in IMDSv1), IMDSv2 requires a valid session token, which makes it more secure for accessing sensitive instance metadata and IAM role credentials.

---

### **Important Notes:**
- **IMDSv2 vs IMDSv1**: You can configure an EC2 instance to allow either only IMDSv2, or both IMDSv1 and IMDSv2. AWS recommends using IMDSv2, but if an instance is still using IMDSv1, you can access metadata without the session token.
  
  To check whether your instance is configured to use IMDSv1, you can check the metadata options in the EC2 console or by using the AWS CLI.

  - To disable IMDSv1 and use only IMDSv2, run the following AWS CLI command:
    ```bash
    aws ec2 modify-instance-metadata-options --instance-id i-xxxxxxxxxxxx --http-tokens required
    ```

- **TTL (Time to Live)**: The session token you request will only be valid for the specified TTL (time-to-live) period. After that, you need to request a new token.

---

### **Conclusion**

This script and explanation demonstrate how to securely access EC2 instance metadata using **IMDSv2** with a session token. The alias helps automate the inclusion of the session token in every `curl` request, making it more convenient to work with metadata in a secure manner.
