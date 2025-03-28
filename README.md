# 🚀 AWS CloudFormation Automation Project

<div style="display: flex; justify-content: center; gap: 15px;">
  <img src="https://github.com/user-attachments/assets/d32023b2-687c-48ed-9dc0-ba7b97b877e4" width="200">
  <img src="https://github.com/user-attachments/assets/63034a5e-1879-4c71-ad4f-1507cc0b6c9a" width="200">
  <img src="https://github.com/user-attachments/assets/307c0840-0e0d-4576-8e0e-e8764bc50304" width="200">
  <img src="https://github.com/user-attachments/assets/bd55efb3-9cb2-4c58-bc08-eb2acfd6a30a" width="200">
</div>

## 📌 Project Overview
This project automates AWS infrastructure deployment using **AWS CloudFormation** templates. It provisions essential cloud resources across three tasks:

1. **Task 1**: Creates a **VPC and Security Group**.
2. **Task 2**: Creates an **S3 Bucket**.
3. **Task 3**: Deploys an **EC2 Instance in a Public Subnet** with security configurations and an S3 bucket.

Each task is managed through a separate CloudFormation YAML template.

---

## 📂 Project Structure
```
cloudformation-automation/
│── templates/
│   ├── task1.yaml    # VPC & Security Group
│   ├── task2.yaml    # S3 Bucket
│   ├── task3.yaml    # EC2 Instance & Dependencies
│── README.md         # Project Documentation
```

---

## 🛠️ Technologies Used
- **AWS CloudFormation** - Infrastructure as Code (IaC)
- **Amazon EC2** - Virtual server deployment
- **Amazon S3** - Storage service
- **AWS CLI** - Command-line interface for managing AWS
- **IAM Roles & Policies** - Security and access management
- **Bash scripting** - Automation commands

---

## 🚀 Deployment Guide
Follow these steps to deploy and verify the infrastructure.

### **Step 1: Set Up AWS CLI**
Ensure you have AWS CLI installed and configured.
```bash
aws configure
```
This command prompts for AWS credentials:

AWS Access Key ID

AWS Secret Access Key

Default region name (e.g., us-east-1)

Default output format (e.g., json)

### **Step 2: Deploy Task 1 (VPC & Security Group)**
```bash
aws cloudformation create-stack \
  --stack-name task1-stack \
  --template-body file://templates/task1.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

✅ **Verify:**
```bash
aws cloudformation describe-stacks --stack-name task1-stack --query "Stacks[0].Outputs" --output table
```

### **Step 3: Deploy Task 2 (S3 Bucket)**
```bash
aws cloudformation create-stack \
  --stack-name task2-stack \
  --template-body file://templates/task2.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

✅ **Verify:**
```bash
aws s3 ls
```

### Step 4: Modify task3.yaml Before Deployment

Before deploying Task 3, make the following modifications:

1. Update the Amazon Linux AMI ID

Find the latest Amazon Linux AMI ID:

```bash
aws ec2 describe-images --owners amazon --filters "Name=name,Values=amzn2-ami-hvm-*-x86_64-gp2" --query 'Images[*].[ImageId,CreationDate]' --output text | sort -k2 -r | head -n 1
```

Replace the existing ImageId in task3.yaml with the latest AMI ID:

```bash
ImageId: <LATEST_AMI_ID>  # Replace with the latest AMI ID for your region
```

2. Modify the Default Web Page Message

Locate the UserData section in task3.yaml and update the HTML content:

```bash
UserData:
  Fn::Base64: !Sub |
    #!/bin/bash
    echo "<html><h1>Welcome to Your AWS CloudFormation Instance</h1></html>" > /var/www/html/index.html
    systemctl start httpd
    systemctl enable httpd
```
Replace "Welcome to Your AWS Cloud Formation instance" with your preferred text.

### **Step 5: Deploy Task 3 (EC2 Instance & Final Setup)**

```bash
aws cloudformation create-stack \
  --stack-name task3-stack \
  --template-body file://templates/task3.yaml \
  --parameters ParameterKey=KeyPairName,ParameterValue=<YOUR-KEY-PAIR-NAME> \
  --capabilities CAPABILITY_NAMED_IAM
```
💡 Replace <YOUR-KEY-PAIR-NAME> with your actual AWS key pair name.

✅ **Verify:**
```bash
aws cloudformation describe-stacks --stack-name task3-stack --query "Stacks[0].Outputs" --output table
```

Retrieve the EC2 Public IP:
```bash
aws ec2 describe-instances --query "Reservations[0].Instances[0].PublicIpAddress" --output text
```

Test with `curl`:
```bash
curl http://<EC2_Public_IP>
```

---

## 🧹 Cleanup: Delete the Stacks
To remove all deployed resources, delete the stacks in reverse order:

```bash
aws cloudformation delete-stack --stack-name task3-stack
aws cloudformation delete-stack --stack-name task2-stack
aws cloudformation delete-stack --stack-name task1-stack
```

✅ **Verify Stack Deletion:**
```bash
aws cloudformation describe-stacks --stack-name task3-stack
```
If the stack is successfully deleted, you should see an error:
```
An error occurred (ValidationError): Stack with id task3-stack does not exist
```

---

## 📸 Screenshots for Documentation
### 1. **Project Structure**
**Label:** "Project Folder Structure"

### 2. **Stack Creation Output**
**Label:** "CloudFormation Stack Creation Output"

### 3. **CloudFormation Outputs**
**Label:** "CloudFormation Stack Outputs"

### 4. **EC2 Instance Running**
**Label:** "EC2 Instance Deployed via CloudFormation"

### 5. **S3 Bucket Listing**
**Label:** "S3 Bucket Created via CloudFormation"

### 6. **curl Test Output**
**Label:** "Web Server Response from EC2 Instance"

### 7. **Stack Deletion Confirmation**
**Label:** "AWS CLI Output After Stack Deletion"

---

## 🎯 Why This Project Stands Out
This project demonstrates:
- **Infrastructure as Code (IaC)**: Automating AWS resource creation with CloudFormation.
- **Scalability & Modularity**: Using separate stacks for different resources.
- **AWS Best Practices**: Security group rules, IAM capabilities, and networking.
- **Automation & Verification**: Using AWS CLI for deployment and testing.

---

🚀 **By following this guide, you can deploy and manage AWS infrastructure efficiently using CloudFormation!**

---
👩‍💻 Author

Diane Ihezue

DevOps & Cloud Engineer 

✨ Feel free to fork this repo, deploy the stack, and experiment on your own!

