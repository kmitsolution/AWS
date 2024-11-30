 **vertical scaling** in AWS EC2 refers to **changing the instance type** of an EC2 instance to a larger or smaller instance type within the same family to adjust for changes in the resource requirements (like CPU, RAM, or network performance) without changing the underlying infrastructure or architecture.

In your example, **changing from `t2.micro` to `t2.medium`** would be a typical example of **vertical scaling**.

### Vertical Scaling: Overview
- **Vertical scaling (Scaling up/down)** involves modifying an existing EC2 instance by changing its instance type to a larger or smaller one. This means adding more resources (CPU, RAM) or reducing resources based on the workload.
- **Scaling up** would mean moving to a larger instance type (e.g., `t2.micro` to `t2.medium`), which provides more **CPU, memory, and networking capacity**.
- **Scaling down** would mean moving to a smaller instance type (e.g., `t2.medium` to `t2.micro`), which reduces resources but also potentially reduces cost.

### When to Use Vertical Scaling
- **When you need more resources** (e.g., more CPU power or memory) for an existing application or workload.
- **When you're scaling to a smaller instance** to reduce costs after a workload decreases or performance requirements change.

### Example of Vertical Scaling (Scaling Up)
- If you have an EC2 instance of type `t2.micro` and your application needs more CPU or memory, you can vertically scale to a larger instance type, like `t2.medium`, to get more vCPUs and memory. 

### **Steps to Change EC2 Instance Type (Vertical Scaling)**
1. **Stop the EC2 Instance**:
   - Before changing the instance type, the instance must be stopped (but not terminated).
   - Go to the **EC2 Console**, select your instance, and click on **Actions > Instance State > Stop**. Wait for the instance to fully stop.

2. **Change the Instance Type**:
   - Once the instance is stopped, you can modify the instance type.
   - Go to **Actions > Instance Settings > Change Instance Type**.
   - Choose the new instance type you want to scale to (e.g., change from `t2.micro` to `t2.medium`).
   
3. **Start the EC2 Instance**:
   - After changing the instance type, click **Actions > Instance State > Start** to start the instance with the new configuration.

4. **Verify the New Instance Type**:
   - Once the instance is running, verify that the new instance type is applied correctly.
   - You can do this by checking the instance details in the EC2 Console or by using the AWS CLI (`aws ec2 describe-instances`).

### **Key Points About Vertical Scaling**
- **Downtime**: Vertical scaling typically involves some downtime because you need to stop the instance to change the instance type.
- **Instance Family**: You can change the instance type within the same instance family (e.g., from `t2.micro` to `t2.medium` or `t2.large`). However, moving to a different family may require additional considerations, such as AMI compatibility and changes in underlying architecture.
- **Elastic Load Balancer (ELB)**: If you are scaling vertically in a production environment, you may need to adjust your load balancing strategy, especially if you're scaling down to avoid performance degradation.
  
### **Vertical Scaling Example:**
- If your EC2 instance of type `t2.micro` (1 vCPU, 1GB RAM) is struggling to handle an increase in traffic, you can scale vertically to `t2.medium` (2 vCPUs, 4GB RAM) to get more resources. This change will give you more computational power to handle the additional load.

### **Advantages of Vertical Scaling**
- **Simple**: It's easy to implement and doesn't require changes to the architecture or deployment methods (e.g., no need to add new instances or configure auto-scaling).
- **Ideal for workloads with predictable traffic**: Vertical scaling is useful when you know your workload will require more or fewer resources and scaling horizontally (adding more instances) isn’t necessary.

### **Disadvantages of Vertical Scaling**
- **Downtime**: As you need to stop and start the instance to change its type, it leads to temporary service downtime.
- **Limits of scalability**: There's a limit to how large you can scale a single instance. Once you've hit the limit of the largest instance type (e.g., `t2.large`), you can’t scale vertically anymore and will have to consider horizontal scaling (adding more instances).
  
### **Horizontal Scaling vs. Vertical Scaling**
- **Horizontal Scaling** (Scaling Out/In): Adding or removing EC2 instances to handle increased or decreased load (e.g., using an Auto Scaling Group).
- **Vertical Scaling** (Scaling Up/Down): Changing the size of a single EC2 instance to handle increased or decreased load.

---

### Conclusion
In summary, **vertical scaling** is the process of upgrading (scaling up) or downgrading (scaling down) the resources of an EC2 instance by changing its instance type. In your example, changing from `t2.micro` to `t2.medium` is a typical case of **vertical scaling** where the instance's resources (CPU, memory, etc.) are adjusted based on the requirements of your application.
