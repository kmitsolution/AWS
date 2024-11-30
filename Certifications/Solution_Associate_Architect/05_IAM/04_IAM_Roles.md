### **AWS Roles Overview**

An **IAM Role** is an AWS identity with specific permissions that define what actions can be performed on AWS resources. Unlike IAM users, roles do not have long-term credentials (e.g., passwords or access keys). Instead, roles are assumed by trusted entities such as **IAM users**, **applications**, or **EC2 instances**.

When you attach a role to an EC2 instance, the instance assumes that role, gaining temporary security credentials to interact with AWS services, based on the policies attached to the role.

### **Creating and Using IAM Roles with EC2 Instances**

In this case study, we will create an IAM role for an EC2 instance that allows the instance to access Amazon S3 (for example). We'll walk through the following steps:

1. **Create an IAM Role for EC2**
2. **Attach the Role to an EC2 Instance**
3. **Provide the Access to EC2 Instance**

---

### **Step 1: Create an IAM Role for EC2 Instance**

#### 1. **Login to the AWS Management Console**:
   - Go to the [IAM Console](https://console.aws.amazon.com/iam/).

#### 2. **Create the Role**:
   - From the left navigation pane, click on **Roles**.
   - Click the **Create role** button at the top of the page.

#### 3. **Select Trusted Entity**:
   - Under the **Select type of trusted entity**, choose **AWS service**.
   - Under **Choose a use case**, select **EC2**.
     - This means the role will be assumed by an EC2 instance.
   - Click **Next: Permissions**.

#### 4. **Attach Permissions Policy**:
   - Now, you will attach permissions to the role. For this case study, we will give the EC2 instance access to Amazon S3.
   - In the search bar, type `AmazonS3FullAccess` to find the built-in policy that grants full access to S3.
   - Check the box next to **AmazonS3FullAccess**.
   - Click **Next: Tags**.

#### 5. **Optional: Add Tags**:
   - You can add tags for easy identification, like `Key: Department` and `Value: DevOps`. However, this step is optional.
   - Click **Next: Review**.

#### 6. **Review and Create Role**:
   - Provide a name for the role. For example, name it `EC2-S3-Access-Role`.
   - Review the settings, and click **Create role**.

   Now, the IAM role `EC2-S3-Access-Role` is created, which grants the EC2 instance access to S3.

---

### **Step 2: Attach the IAM Role to an EC2 Instance**

Now that we have the role, we need to attach it to an EC2 instance.

#### 1. **Launch a New EC2 Instance** (if you do not have one running):
   - Go to the **EC2 Console**.
   - Click on **Launch Instances** to start the process of creating a new EC2 instance.
   - Choose an **Amazon Machine Image (AMI)** (for example, Amazon Linux 2 or Ubuntu).
   - Choose an **Instance Type** (e.g., `t2.micro`).
   - Follow through the setup steps, and when you reach the **Configure Instance** step:

     - In the **IAM role** section, select the role you created earlier (`EC2-S3-Access-Role`).
     - Continue the remaining steps and launch the instance.

#### 2. **Attach the Role to an Existing EC2 Instance**:
   If you want to attach the role to an **existing instance**:

   - In the **EC2 Console**, select the instance you want to assign the role to.
   - In the **Description** section, click **Actions** → **Security** → **Modify IAM Role**.
   - Under **IAM role**, select the role you created (`EC2-S3-Access-Role`).
   - Click **Update IAM role**.

   This will attach the `EC2-S3-Access-Role` to your existing EC2 instance.

---

### **Step 3: Provide the Access to EC2 Instance**

To verify that the EC2 instance now has access to Amazon S3, you can SSH into the instance and try accessing an S3 bucket.

#### 1. **SSH into the EC2 Instance**:

- For **Linux**/**Ubuntu** instances, use the following SSH command from your local machine:
   ```bash
   ssh -i /path/to/your-key.pem ec2-user@your-ec2-public-ip
   ```

- For **Windows** instances, use an RDP client to log in.

#### 2. **Check S3 Access from EC2**:

   Once logged in, you can use the **AWS CLI** to test if the EC2 instance has S3 access.

   First, verify that AWS CLI is installed by typing:
   ```bash
   aws --version
   ```

   Then, to check if the EC2 instance has permission to access S3, run the following command:
   ```bash
   aws s3 ls
   ```

   If the instance has the right permissions, it will list the S3 buckets you have access to.

   Example output (if the instance has S3 access):
   ```bash
   2024-01-01 12:34:56 my-bucket
   2024-02-01 14:23:45 another-bucket
   ```

   If the instance does **not** have permissions, you will receive an error message indicating the lack of permission.

---

### **Conclusion**

By following these steps, you’ve:

1. **Created an IAM Role** that grants EC2 instances access to Amazon S3.
2. **Attached the IAM Role** to an EC2 instance, either when launching a new instance or by modifying an existing one.
3. **Verified Access** by SSHing into the EC2 instance and running an AWS CLI command to list S3 buckets.

This demonstrates how to use IAM roles to securely manage access permissions for EC2 instances, without the need to manage and distribute access keys directly to the instances. You can apply the same process to provide access to other AWS services (like S3, DynamoDB, etc.) by attaching the corresponding policies to the IAM role.
