# Deployment Guide

## Prerequisites
- AWS CLI installed and configured with appropriate permissions
- SSH key pair for EC2 instances
- Basic understanding of AWS services (VPC, EC2, ELB, Auto Scaling)

## Step-by-Step Deployment Process

### 1. VPC Setup
1. Create VPC

aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=WebAppVPC}]'

2. Create subnets (public and private) in two availability zones
3. Create and attach Internet Gateway
4. Create NAT Gateway in a public subnet
5. Configure route tables for public and private subnets

### 2. Security Group Configuration
1. Create Load Balancer Security Group
2. Create Web Server Security Group
3. Create Bastion Host Security Group

### 3. Launch Bastion Host
1. Launch EC2 instance in public subnet using Bastion-SG
2. Configure SSH access as per bastion_host_setup.md

### 4. Application Load Balancer Setup
1. Create Target Group
2. Create Application Load Balancer
3. Configure Listener

### 5. Auto Scaling Group Configuration
1. Create Launch Template

aws ec2 create-launch-template --launch-template-name WebAppLaunchTemplate --version-description v1 --launch-template-data file://launch-template-data.json

2. Create Auto Scaling Group

aws autoscaling create-auto-scaling-group --auto-scaling-group-name WebAppASG --launch-template LaunchTemplateName=WebAppLaunchTemplate,Version='$Latest' --min-size 2 --max-size 4 --desired-capacity 2 --vpc-zone-identifier "subnet-xxxxx,subnet-yyyyy" --target-group-arns arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/WebAppTG/1234567890123456

### 6. Application Deployment
1. SSH into instances via bastion host
2. Deploy your web application (e.g., using user data script)

### 7. Testing and Validation
1. Access application via Load Balancer DNS name
2. Test auto scaling by increasing load
3. Verify security group rules are working as expected

## Troubleshooting
- Check VPC Flow Logs for network issues
- Review CloudWatch Logs for application errors
- Verify Security Group rules if unable to connect to instances

## Clean Up
To avoid unnecessary charges, remember to delete resources when no longer needed:
1. Delete Auto Scaling Group
2. Terminate EC2 instances
3. Delete Load Balancer and Target Group
4. Delete NAT Gateway and release Elastic IP
5. Delete VPC and associated resources