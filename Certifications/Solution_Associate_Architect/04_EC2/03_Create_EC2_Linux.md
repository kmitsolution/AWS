Creating and accessing an EC2 Linux instance (Ubuntu/Red Hat) involves several steps. Here's a detailed guide on how to create an EC2 instance and access it from different environments like PuTTY, MobaXterm, AWS CloudShell, and macOS.

### Step 1: Launch EC2 Instance (Ubuntu/RedHat)
1. **Log in to AWS Console**:
   - Go to [AWS Management Console](https://aws.amazon.com/console/) and log in with your AWS credentials.

2. **Launch Instance**:
   - In the AWS Console, navigate to **EC2** under the "Services" section.
   - Click on **Launch Instance** to start the creation process.

3. **Select AMI (Amazon Machine Image)**:
   - Select an appropriate **AMI** (e.g., Ubuntu or Red Hat) under the "Quick Start" or "AWS Marketplace" section.
     - For Ubuntu: Choose an AMI like **Ubuntu Server 20.04 LTS**.
     - For Red Hat: Choose an AMI like **Red Hat Enterprise Linux**.

4. **Choose Instance Type**:
   - Select the desired instance type based on your requirements (e.g., `t2.micro` for free tier eligible).
   - Click **Next: Configure Instance Details**.

5. **Configure Instance**:
   - You can leave most settings as default, but ensure that you configure networking and other settings based on your needs.
   - Click **Next: Add Storage**.

6. **Add Storage**:
   - You can modify the storage size if needed. The default size is typically enough for most use cases.
   - Click **Next: Add Tags**.

7. **Add Tags**:
   - You can add tags to help organize your instance, though this step is optional.
   - Click **Next: Configure Security Group**.

8. **Configure Security Group**:
   - Create a new security group to control access to the instance.
   - Add a rule for **SSH (port 22)**:
     - Type: SSH
     - Protocol: TCP
     - Port Range: 22
     - Source: **Anywhere** (or specify your IP address for security)
   - Click **Review and Launch**.

9. **Key Pair**:
   - **Create a new key pair** or use an existing one:
     - Select **Create a new key pair**.
     - Give it a name and download the `.pem` file (important for SSH access).
     - Keep the `.pem` file in a safe location.
   - Click **Launch Instances** to finish the process.

10. **Instance Running**:
   - Once the instance is created, you’ll see it in the EC2 dashboard under **Instances**.
   - Wait for the instance's **Status** to show "running."

### Step 2: Access the EC2 Instance
To access your EC2 instance from different environments (Windows, macOS, AWS CloudShell), follow these instructions.

#### Accessing EC2 Instance from **PuTTY (Windows)**
1. **Convert `.pem` to `.ppk`**:
   - PuTTY doesn’t support `.pem` files, so you must convert it to `.ppk` format using **PuTTYgen**.
   - Open **PuTTYgen** (comes with PuTTY).
   - Click **Load** and select your `.pem` file.
   - Click **Save private key** and save the `.ppk` file.

2. **Connect Using PuTTY**:
   - Open **PuTTY** and enter your instance's **Public IP** or **Public DNS** under the **Host Name (or IP address)** field.
   - Under **Connection > SSH > Auth**, browse and select your **.ppk** file.
   - Click **Open**.
   - The first time you connect, you’ll see a security alert. Click **Yes**.
   - Login as the default user:
     - For **Ubuntu**: `ubuntu`
     - For **RedHat**: `ec2-user`

#### Accessing EC2 Instance from **MobaXterm (Windows)**
1. **Prepare MobaXterm**:
   - Download and install [MobaXterm](https://mobaxterm.mobatek.net/download-home-edition.html).
   
2. **Configure MobaXterm**:
   - Click **Session** in MobaXterm and select **SSH**.
   - In the **Remote host** field, enter your EC2 instance's **Public IP** or **Public DNS**.
   - In the **Specify username** field, enter:
     - For **Ubuntu**: `ubuntu`
     - For **RedHat**: `ec2-user`
   - Under the **Advanced SSH settings**, enable **Use private key** and browse for your **.pem** file.
   
3. **Connect**:
   - Click **OK** to connect.
   - Accept the SSH key fingerprint when prompted and you're logged in to your instance.

#### Accessing EC2 Instance from **AWS CloudShell**
1. **Open AWS CloudShell**:
   - In the AWS Console, open **CloudShell** by clicking on the CloudShell icon in the top-right corner.
   
2. **Access EC2 Instance**:
   - CloudShell has SSH capabilities built-in, so you don’t need to set up an external SSH client.
   - Use the following command to connect to your EC2 instance:
     ```bash
     ssh -i /path/to/your-key.pem ubuntu@your-instance-public-ip
     ```
     - Ensure your **.pem** file is uploaded to CloudShell or use a CloudShell-managed key.
     - For **Ubuntu**: Use `ubuntu` as the username.
     - For **RedHat**: Use `ec2-user`.

#### Accessing EC2 Instance from **macOS**
1. **Open Terminal**:
   - Open **Terminal** on macOS.

2. **Set Permissions for .pem File**:
   - If you haven’t already, ensure the permissions for the **.pem** file are correct:
     ```bash
     chmod 400 /path/to/your-key.pem
     ```

3. **SSH Command**:
   - Use the following command to connect:
     ```bash
     ssh -i /path/to/your-key.pem ubuntu@your-instance-public-ip
     ```
     - For **Ubuntu**: `ubuntu` is the username.
     - For **RedHat**: `ec2-user` is the username.

### Additional Notes:
- **Security Groups**: Ensure your EC2 security group allows inbound SSH traffic (port 22) from your IP address.
- **Instance State**: Make sure the instance is in the **running** state, and you have the correct **public IP** or **public DNS**.
- **IAM Role**: If you're using AWS CloudShell or any other AWS service to access EC2, ensure that the IAM role associated with the service has the necessary permissions to interact with EC2 instances.

By following these steps, you should be able to successfully create and access your EC2 instance on various platforms.
