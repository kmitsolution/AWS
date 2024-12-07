### **5. EBS Snapshots**

Amazon Elastic Block Store (EBS) Snapshots are point-in-time backups of your EBS volumes. Snapshots are incremental, meaning only the changes made since the last snapshot are saved. They provide a reliable way to backup and restore your data.

---

### **Creating Snapshots**

EBS snapshots are used for backup purposes, disaster recovery, or creating new volumes from an existing state. Here's how to create and manage EBS snapshots.

#### **Steps to Create a Snapshot of an EBS Volume:**

1. **Navigate to the EC2 Dashboard:**
   - In the AWS Management Console, go to **EC2 Dashboard**.

2. **Select the Volume:**
   - In the **Elastic Block Store** section, click on **Volumes**.
   - Select the volume for which you want to create a snapshot.

3. **Create a Snapshot:**
   - Click on **Actions** > **Create Snapshot**.

4. **Enter Snapshot Details:**
   - **Name**: Provide a name for the snapshot (e.g., `Backup-Snapshot-1`).
   - **Description**: Optionally, add a description for the snapshot (e.g., `Snapshot before the latest system update`).

5. **Create the Snapshot:**
   - Click **Create Snapshot**. AWS will begin creating the snapshot. The snapshot will appear in the **Snapshots** section of **Elastic Block Store**.

6. **Monitor Snapshot Creation:**
   - You can monitor the snapshot creation process. The status will change from `pending` to `completed` once the snapshot is ready.

#### **Using AWS CLI to Create a Snapshot:**

You can also create an EBS snapshot using AWS CLI. The following command creates a snapshot of the specified EBS volume:

```bash
aws ec2 create-snapshot --volume-id vol-1234567890abcdef0 --description "Backup before maintenance"
```

- **`--volume-id`**: The ID of the volume you want to snapshot.
- **`--description`**: A description for the snapshot (optional).

Once the snapshot is created, you can see the snapshot ID and status.

---

### **Restoring from Snapshots**

Restoring from snapshots is helpful if you need to recover data or create new volumes based on previous states.

#### **Steps to Restore from an EBS Snapshot:**

1. **Create a New Volume from Snapshot:**
   - Go to the **Snapshots** section of **Elastic Block Store** in the EC2 Dashboard.
   - Select the snapshot you want to restore from.
   - Click **Actions** > **Create Volume**.
   - Choose the desired **Availability Zone**, and adjust other settings such as volume type or size if needed.
   - Click **Create Volume** to create a new volume based on the snapshot.

2. **Attach the New Volume to an EC2 Instance:**
   - Once the volume has been created, go to the **Volumes** section.
   - Select the new volume and click on **Actions** > **Attach Volume**.
   - Select the EC2 instance to which you want to attach the volume.
   - Assign a device name (e.g., `/dev/sdf`) and click **Attach**.

3. **Use the Volume for Restoring or Accessing Data:**
   - Once attached, you can mount the volume on the EC2 instance (if it contains a file system) or use it to restore data.

   - For example, to mount the volume, you can follow these steps:
     - Create a directory to mount the volume:
       ```bash
       sudo mkdir /mnt/restored_volume
       ```
     - Mount the volume:
       ```bash
       sudo mount /dev/xvdf /mnt/restored_volume
       ```
     - Verify the contents of the mounted volume:
       ```bash
       ls /mnt/restored_volume
       ```

---

### **Key Concepts of EBS Snapshots:**

- **Incremental Snapshots**: EBS snapshots are incremental. This means that after the first snapshot, only the data that has changed since the last snapshot is saved, saving time and storage costs.
  
- **Cross-Region Snapshots**: You can copy snapshots to other AWS regions for disaster recovery, compliance, or backup purposes.

- **Restore Data**: Snapshots allow you to restore data to a previous state by creating new volumes from the snapshot.

- **Cost-Effective**: Snapshots only incur costs based on the data stored, making them a cost-effective backup solution.

---

### **Example: Restoring Data from a Snapshot**

1. **Create a Snapshot of an Existing Volume**:
   - Create a snapshot for the volume `/dev/xvdf` using the AWS Console or AWS CLI.

2. **Create a New Volume from Snapshot**:
   - After the snapshot is completed, create a new volume from it and choose the same Availability Zone as the EC2 instance.

3. **Attach the New Volume to the EC2 Instance**:
   - Attach the volume to the EC2 instance, say as `/dev/xvdg`.

4. **Mount the Volume and Access Data**:
   - Mount the volume to the directory `/mnt/restored_volume` and verify the restored data:
     ```bash
     sudo mount /dev/xvdg /mnt/restored_volume
     ls /mnt/restored_volume
     ```

5. **Access and Use the Restored Data**:
   - The data in the new volume is identical to the data at the time of the snapshot.

---

### **Snapshot Lifecycle Management**

- **Automated Snapshots**: You can set up automated snapshot schedules using **AWS Data Lifecycle Manager** to create and delete snapshots based on predefined policies.
- **Snapshot Deletion**: Snapshots can be deleted via the AWS Console or using the AWS CLI:
  ```bash
  aws ec2 delete-snapshot --snapshot-id snap-1234567890abcdef0
  ```

---

### **Summary: EBS Snapshots**

| **Action**                        | **Command**                                | **Description**                                                                 |
|-----------------------------------|--------------------------------------------|---------------------------------------------------------------------------------|
| **Create Snapshot**               | `aws ec2 create-snapshot`                 | Create a point-in-time backup of the EBS volume.                                 |
| **Create Volume from Snapshot**   | `aws ec2 create-volume --snapshot-id`     | Create a new volume based on an existing snapshot.                              |
| **Attach Volume to EC2**          | `aws ec2 attach-volume`                   | Attach the new volume to an EC2 instance.                                       |
| **Mount Restored Volume**         | `mount /dev/xvdf /mnt/restored_volume`     | Mount the restored volume to an EC2 instance for use.                           |
| **Delete Snapshot**               | `aws ec2 delete-snapshot`                 | Remove a snapshot that is no longer needed.                                     |

By using EBS snapshots, you can easily back up and restore your data on EC2 instances, providing a reliable disaster recovery solution.
