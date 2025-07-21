# PROJECT 2

# ☁️ EC2 to S3 Automated Backup using IAM Role

This project demonstrates how to securely automate file backups from an EC2 instance to an S3 bucket using an IAM Role — **without using access keys**.  
It includes shell scripting, AWS CLI, IAM policies, and cron job scheduling.

---

## ✅ What This Project Demonstrates

- EC2 + S3 integration
- IAM Role with scoped permissions (`s3:PutObject`)
- AWS CLI usage inside EC2
- Automation with Bash script + `cron`
- Secure backup workflow

---

## 🧠 Overview

An EC2 instance uploads files from a local directory (`~/meus-arquivos`) to an S3 bucket (`ec2-backup-demo-vivaldo`) daily at 2:00 AM using a cron job.

---

## 🛠️ Step-by-Step Setup

### 1. Create an S3 Bucket

- Go to **S3 > Create bucket**
- Name: `create a name for your bucket`
- Enable versioning or encryption if desired

---

### 2. Create an IAM Policy

Go to **IAM > Policies > Create policy > JSON**, and paste:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::nome-do-seu-bucket/*"
    }
  ]
}
```

### 3. Create an IAM Role for EC2

- Go to **IAM > Roles > Create role**
- Trusted entity: **EC2**
- Attach the custom policy created above
- Name: `Create a name for your role`

---

### 4. Launch an EC2 Instance

- AMI: **Amazon Linux 2** or **Amazon Linux 2023**
- Instance type: `t2.micro`
- Attach IAM Role: `Choose the name of your role`
- Open **port 22 (SSH)** for access
- Create or select a **Key Pair** (e.g. `ec2-s3-backup-keypair`)

---

### 5. Connect via SSH

```bash
chmod 400 ec2-s3-backup-keypair.pem
ssh -i ec2-s3-backup-keypair.pem ec2-user@<your-ec2-public-ip>
```

---

### 6. Create a Sample Backup Directory and File

```bash
mkdir ~/my-files
echo "Backup done in $(date)" > ~/my-files/backup.log
```

---

### 7. Verify AWS CLI Installation

```bash
aws --version
```

If not installed:

```bash
sudo dnf install cronie aws-cli -y         # Amazon Linux 2023
sudo yum install aws-cli -y               # Amazon Linux 2
```

---

### 8. Test S3 Access from EC2

```bash
aws s3 cp ~/my-files/ s3://your ec2 name/ --recursive
```

Expected output:

```
upload: ./backup.log to s3://your ec2 name/backup.log
```

---

### 9. Create a Backup Script

```bash
nano ~/backup-s3.sh
```

Paste the following:

```bash
#!/bin/bash
/usr/bin/aws s3 cp ~/my-files/ s3://your ec2 name/ --recursive
```

Then make it executable:

```bash
chmod +x ~/backup-s3.sh
```

---

### 10. Enable and Start the Cron Service (Amazon Linux 2023 only)

```bash
sudo systemctl enable crond
sudo systemctl start crond
```

---

### 11. Schedule the Backup with Crontab

```bash
crontab -e
```

Add this line to run the backup daily at 2:00 AM:

```bash
0 2 * * * /home/ec2-user/backup-s3.sh >> /home/ec2-user/cron.log 2>&1
```

✅ This logs the output to `cron.log`.

---

## 🔍 Verifying Cron Job Execution

Check if the cron ran successfully:

```bash
cat /home/ec2-user/cron.log
```

You should see lines like:

```
upload: ./backup.log to s3://your ec2 name/backup.log
```

---

## 🧹 Stopping or Editing the Cron Job

To pause or stop the cron job:

```bash
crontab -e
```

- Comment the line with `#` to disable temporarily
- Or delete the line to remove it completely

---

## 📁 Extra: Create a .txt file using `nano` in a folder

```bash
mkdir -p ~/my-files
nano ~/my-files/backup-info.txt
```

Write your notes, then save with:

- `Ctrl + O` → Save
- `Enter` → Confirm
- `Ctrl + X` → Exit

---

## ✅ Final Result

Your EC2 instance now performs **secure, automated, daily backups** to your S3 bucket using a custom IAM Role — **no access keys required**.

This project demonstrates cloud automation, scripting, IAM security, and real-world AWS usage.

---

## 🧠 Lessons Learned

- How to assign IAM roles to EC2 instances for secure access to S3
- How to write a shell script to automate backups to an S3 bucket
- How to schedule recurring tasks using cron on Amazon Linux
- The importance of restricting IAM policies to specific actions and resources
- How to verify AWS CLI functionality inside an EC2 instance
- How to securely upload files to S3 without using access keys
- How to log and monitor scheduled scripts with output redirection

## 📫 Contact

- GitHub: [github.com/vivaldosantanawebdev](https://github.com/vivaldosantanawebdev)
- LinkedIn: [linkedin.com/in/vivaldo-santana](https://www.linkedin.com/in/vivaldo-santana/)
