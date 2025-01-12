# Automating-EC2-instance-management-using-AWS-CLI

## EC2 Automation Challenge

This repository contains a hands-on challenge to automate EC2 instance management using AWS CLI. The challenge involves launching instances, creating and attaching EBS volumes, stopping and terminating instances based on tags, and managing security groups and key pairs.

## Challenge Objectives

1. **Launch Multiple EC2 Instances** with a custom AMI and appropriate tags.
2. **Create and Attach EBS Volumes** to these instances.
3. **Stop and Terminate Instances** with a specific tag using a script.
4. **Manage Security Groups and Key Pairs** through AWS CLI.

## Prerequisites

- AWS CLI installed and configured.
- A custom AMI available in your AWS account.
- Appropriate IAM permissions to manage EC2 and related resources.

## Step-by-Step Guide

### 1. Launch Multiple EC2 Instances 
Launch three EC2 instances with a specific custom AMI, using the following command:
```Variables
AMI_ID="ami-xxxxxxxxxxxxxx"  # Replace with your custom AMI ID
INSTANCE_TYPE="t2.micro"      # Choose an instance type
KEY_NAME="my-key-pair"        # Name of the key pair
SECURITY_GROUP="sg-xxxxxxxxxx" # Security group ID
TAG_KEY="Environment"
TAG_VALUE="Dev"
```

```Launch instances using bash
aws ec2 run-instances --image-id $AMI_ID --count 3 --instance-type $INSTANCE_TYPE \
--key-name $KEY_NAME --security-group-ids $SECURITY_GROUP \
--tag-specifications "ResourceType=instance,Tags=[{Key=$TAG_KEY,Value=$TAG_VALUE}]" \
--query 'Instances[*].InstanceId' --output text
```

```Example
aws ec2 run-instances --image-id ami-xxxxxxxxxxxxxx --count 3 --instance-type t2.micro \
--key-name my-key-pair --security-group-ids sg-xxxxxxxxxx \
--tag-specifications "ResourceType=instance,Tags=[{Key=Environment,Value=Dev}]"
```

### 2. Create and Attach EBS Volumes
Create three 10GB EBS volumes and attach each to the instances:

```bash
aws ec2 create-volume --size 10 --region af-south-1 --availability-zone af-south-1a --volume-type gp2
```

Attach the volumes using:
```
aws ec2 attach-volume --volume-id vol-xxxxxxxxxxxxx --instance-id i-xxxxxxxxxxxxx --device /dev/xvdf
```

### 3. Stop and Terminate Instances with a Specific Tag
Stop and terminate instances with the Environment=Dev tag:

```bash
aws ec2 describe-instances --filters "Name=tag:Environment,Values=Dev" \
--query 'Reservations[*].Instances[*].InstanceId' --output text | xargs -n1 aws ec2 stop-instances --instance-ids
```

### 4. Manage Security Groups and Key Pairs
Create a security group and a key pair:

```
aws ec2 create-security-group --group-name my-sg --description "My security group"
aws ec2 create-key-pair --key-name my-key-pair --query 'KeyMaterial' --output text > my-key-pair.pem
```

### Conclusion
This challenge helps you understand and automate common EC2 instance management tasks using AWS CLI. Feel free to extend this repository with more automation scripts and best practices.
