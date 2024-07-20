# Security Group Definitions

## Overview
This document outlines the security group configurations for our web application infrastructure.

## Security Groups

### 1. Load Balancer Security Group
- **Name**: ALB-SG
- **Inbound Rules**:
  - Allow HTTP (80) from 0.0.0.0/0
  - Allow HTTPS (443) from 0.0.0.0/0
- **Outbound Rules**:
  - Allow all traffic to 0.0.0.0/0

### 2. Web Server Security Group
- **Name**: WebApp-SG
- **Inbound Rules**:
  - Allow HTTP (80) from ALB-SG
  - Allow SSH (22) from Bastion-SG
- **Outbound Rules**:
  - Allow all traffic to 0.0.0.0/0

### 3. Bastion Host Security Group
- **Name**: Bastion-SG
- **Inbound Rules**:
  - Allow SSH (22) from [Your office IP range]
- **Outbound Rules**:
  - Allow SSH (22) to WebApp-SG
  - Allow all traffic to 0.0.0.0/0

## Implementation Steps
1. Create Load Balancer Security Group

aws ec2 create-security-group --group-name ALB-SG --description "Security group for Application Load Balancer" --vpc-id vpc-0123456789abcdef0
aws ec2 authorize-security-group-ingress --group-id sg-12345678 --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id sg-12345678 --protocol tcp --port 443 --cidr 0.0.0.0/0

2. Create Web Server Security Group

aws ec2 create-security-group --group-name WebApp-SG --description "Security group for Web Servers" --vpc-id vpc-0123456789abcdef0
aws ec2 authorize-security-group-ingress --group-id sg-23456789 --protocol tcp --port 80 --source-group sg-12345678
aws ec2 authorize-security-group-ingress --group-id sg-23456789 --protocol tcp --port 22 --source-group sg-34567890

3. Create Bastion Host Security Group

aws ec2 create-security-group --group-name Bastion-SG --description "Security group for Bastion Host" --vpc-id vpc-0123456789abcdef0
aws ec2 authorize-security-group-ingress --group-id sg-34567890 --protocol tcp --port 22 --cidr your-office-ip-range/32

## Security Best Practices
- Regularly audit and update security group rules
- Use the principle of least privilege
- Avoid using 0.0.0.0/0 as a source in production environments
- Use security groups as sources instead of IP ranges where possible
- Implement network ACLs for additional subnet-level security

## Monitoring and Compliance
- Enable VPC Flow Logs to monitor traffic
- Regularly review security group rules for compliance
- Use AWS Config to track security group changes

s