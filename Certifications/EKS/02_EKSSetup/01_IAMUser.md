To create an IAM user with **EKS Cluster Admin Access** and provide the necessary **Access Key** and **Secret Key**, follow these steps in AWS:

---

### **Steps to Create an IAM User for EKS Cluster Admin Access**

#### **1. Sign In to AWS Management Console**
- Go to the [AWS Management Console](https://aws.amazon.com/console/) and sign in with your account.

#### **2. Navigate to IAM (Identity and Access Management)**
- In the AWS Management Console, search for **IAM** in the search bar and select **IAM** to open the IAM dashboard.

#### **3. Create a New IAM User**
- In the IAM dashboard, click on **Users** in the left-hand menu.
- Click on the **Add user** button at the top of the page.

#### **4. Specify User Details**
- **User Name**: Enter a name for the IAM user (e.g., `eks-admin-user`).
- **Access Type**: Check the **Programmatic access** checkbox to enable API, CLI, and SDK access.
  - This option will provide the **Access Key** and **Secret Key**.
- Click **Next: Permissions**.

#### **5. Attach Policies for EKS Admin Access**
You need to assign the appropriate permissions for EKS cluster management. You can either attach predefined policies or create a custom policy.

##### **Option 1: Attach Managed Policies**
- In the **Set permissions** page, choose the **Attach policies directly** tab.
- Search for the **AmazonEKSClusterPolicy** and **AmazonEKSServicePolicy** managed policies. These policies provide permissions to manage and interact with EKS clusters.
  - **AmazonEKSClusterPolicy**: Provides access to EKS clusters.
  - **AmazonEKSServicePolicy**: Provides permissions for EKS service management.
- Optionally, you can attach **AdministratorAccess** if the user needs full access to all AWS resources (which can be risky for production environments).
  
##### **Option 2: Use a Custom Policy (Optional)**
If you prefer to create a custom policy, click **Create policy** and add permissions based on your specific needs. Here’s an example of a custom policy for EKS cluster admin access:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "eks:*",
        "ec2:Describe*",
        "iam:ListRoles",
        "iam:PassRole",
        "autoscaling:DescribeAutoScalingGroups"
      ],
      "Resource": "*"
    }
  ]
}
```
- After creating the custom policy, attach it to the user.

Click **Next: Tags**.

#### **6. Add Tags (Optional)**
- You can add tags to the user for identification purposes, but this step is optional.
- Click **Next: Review**.

#### **7. Review and Create the User**
- Review the user configuration and permissions.
- If everything looks good, click **Create user**.

#### **8. Download or Save the Access Key and Secret Key**
- After the user is created, a confirmation screen will appear with the user’s **Access Key ID** and **Secret Access Key**.
  - **Important**: This is the only time you will be able to view or download the **Secret Access Key**.
  - Click the **Download .csv** button to save the credentials (or copy them manually to a secure location).

---

### **9. Configure AWS CLI or SDK with the New IAM User**
Once you have the **Access Key ID** and **Secret Access Key**, you can configure the AWS CLI or SDK with these credentials:

#### **To Configure AWS CLI:**
- Open the terminal (Linux/macOS) or Command Prompt (Windows).
- Run the following command to configure the AWS CLI with the new user’s credentials:
```bash
aws configure
```
- You will be prompted to enter:
  - **AWS Access Key ID**: Enter the Access Key ID from the IAM user.
  - **AWS Secret Access Key**: Enter the Secret Access Key from the IAM user.
  - **Default region name**: Enter the AWS region you plan to use (e.g., `us-west-2`).
  - **Default output format**: You can leave this as `json` or choose another format.

Once configured, the IAM user with **EKS Cluster Admin Access** will be able to manage EKS clusters using the AWS CLI, SDK, or API.

---

### **10. Verify Permissions**
To verify that the IAM user has the required permissions, you can use the following AWS CLI command to list the available EKS clusters:
```bash
aws eks list-clusters
```
If the user has the correct permissions, this will return a list of EKS clusters.

---

### **Conclusion**
By following these steps, you have created an IAM user with **EKS Cluster Admin Access** and obtained the **Access Key ID** and **Secret Access Key**. The user can now manage EKS clusters and perform tasks like deploying applications, managing services, and scaling resources in EKS.

