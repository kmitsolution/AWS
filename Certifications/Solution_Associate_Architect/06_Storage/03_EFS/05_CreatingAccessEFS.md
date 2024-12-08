### **Creating and Accessing Amazon E Elastic File System (EFS) with EC2 Instances**

To set up Amazon EFS, create the file system, mount it on EC2 instances, and configure inbound rules for NFS, follow the detailed steps outlined below:

---

### **Step 1: Create an EFS File System**
1. **Log in to AWS Console**:
   - Navigate to the [AWS Management Console](https://aws.amazon.com/console/).
   - Select **EFS** from the Services menu.

2. **Create the File System**:
   - Click on **Create file system**.
   - Choose **VPC** (Virtual Private Cloud) that contains your EC2 instances. It should be the same VPC where your EC2 instances are located.
   - Select **Performance mode** (General Purpose or Max I/O as per your requirements).
   - Select **Storage class** (Standard or One Zone).
   - Click **Next** to proceed with default options or customize based on your needs.
   - Create **Mount Targets**:
     - Choose **Create mount targets** in the appropriate Availability Zones (AZs) that your EC2 instances are located.
     - Attach a **Security Group** for access control. You will add inbound rules in this step.
     - Ensure **NFS** is enabled and configure the proper security group for EC2 access.

3. **Create the File System**:
   - After completing the above configurations, click **Create file system**.
   - Note down the **DNS name** and **Mount Target IPs** for each Availability Zone.

---

### **Step 2: Create and Configure EC2 Instances**

1. **Create an EC2 Instance**:
   - Go to the **EC2 Dashboard** in the AWS Console.
   - Click **Launch Instance** and select an Amazon Machine Image (AMI), such as **Amazon Linux 2**.
   - Choose an **Instance Type** (e.g., t2.micro or larger, based on your requirements).
   - Configure **Network** and **Subnet** to be within the same VPC as your EFS file system.
   - Add a **Security Group** with **Inbound Rule** allowing NFS traffic (port 2049).
   - Select **Key Pair** for SSH access.
   - Click **Launch** to create the instance.

2. **SSH into the EC2 Instance**:
   - Use SSH to connect to the EC2 instance.
     ```
     ssh -i /path/to/your-key.pem ec2-user@<ec2-public-ip>
     ```

3. **Install NFS Utilities**:
   - Install the `amazon-efs-utils` package to mount the EFS file system.
     ```
     sudo yum install -y amazon-efs-utils
     ```

4. **Create a Directory for Mounting EFS**:
   - Create a directory to mount the EFS file system.
     ```
     sudo mkdir /mnt/efs
     ```

5. **Mount the EFS File System**:
   - Mount the EFS file system to the directory created.
     ```
     sudo mount -t efs <file-system-id>:/ /mnt/efs
     ```
     Replace `<file-system-id>` with the ID of the EFS file system you created.

6. **Verify Mount**:
   - Check that the EFS file system is successfully mounted.
     ```
     df -h
     ```
   - You should see the EFS file system mounted at `/mnt/efs`.

7. **Add the Mount to /etc/fstab**:
   - To ensure the EFS file system is mounted automatically on instance reboot, add the following line to `/etc/fstab`:
     ```
     <file-system-id>:/ /mnt/efs efs defaults,_netdev 0 0
     ```

---

### **Step 3: Configure Security Group Inbound Rules for NFS**
1. **Modify Inbound Rules for Security Group**:
   - Go to the **EC2 Dashboard** > **Security Groups**.
   - Select the security group attached to your EC2 instances.
   - Under the **Inbound rules**, click **Edit inbound rules**.
   - Add a rule to allow NFS traffic:
     - **Type**: NFS
     - **Protocol**: TCP
     - **Port Range**: 2049
     - **Source**: **Custom** (select the security group for your EC2 instances)
   - Save the changes.

---

### **Step 4: Attach EFS to Another EC2 Instance**

1. **Create Another EC2 Instance**:
   - Repeat the steps above to launch a second EC2 instance in the same VPC and subnet.

2. **SSH into the Second EC2 Instance**:
   - SSH into the second EC2 instance.
     ```
     ssh -i /path/to/your-key.pem ec2-user@<ec2-public-ip>
     ```

3. **Install NFS Utilities**:
   - Install the `amazon-efs-utils` package on the second EC2 instance.
     ```
     sudo yum install -y amazon-efs-utils
     ```

4. **Create a Directory for Mounting EFS**:
   - Create a directory to mount the EFS file system.
     ```
     sudo mkdir /mnt/efs
     ```

5. **Mount the EFS File System**:
   - Mount the EFS file system on the second EC2 instance.
     ```
     sudo mount -t efs <file-system-id>:/ /mnt/efs
     ```
     Replace `<file-system-id>` with the EFS file system ID.

6. **Verify Mount**:
   - Verify that the file system is mounted on the second EC2 instance.
     ```
     df -h
     ```
     You should see the EFS file system mounted at `/mnt/efs`.

---

### **Step 5: Verify Shared Access**

1. **Test Shared File Access**:
   - On **EC2 Instance 1**, create a test file in the mounted EFS directory:
     ```
     echo "Hello from EC2 Instance 1" > /mnt/efs/testfile.txt
     ```
   
2. **Check the File on EC2 Instance 2**:
   - On **EC2 Instance 2**, verify that the file is accessible:
     ```
     cat /mnt/efs/testfile.txt
     ```
   - You should see the message: `Hello from EC2 Instance 1`.

This confirms that both EC2 instances can access and share data on the same EFS file system.

---

### **Step 6: Clean Up**
After completing your work, you can clean up resources to avoid unnecessary charges:
- **Terminate EC2 Instances**.
- **Delete the EFS File System** if no longer needed.
- **Remove Security Group** and other associated resources.

This setup ensures that multiple EC2 instances can seamlessly share and access the same data using Amazon EFS, making it ideal for collaborative, scalable, and resilient applications.
