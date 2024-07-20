# Deployment Guide

## Prerequisites
- AWS CLI installed and configured with appropriate permissions
- SSH key pair for EC2 instances
- Basic understanding of AWS services (VPC, EC2, ELB, Auto Scaling)

## Step-by-Step Deployment Process

### 1. VPC Setup
1. Create VPC

aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=WebAppVPC}]'

![VPC](https://github.com/user-attachments/assets/7cf3ee24-c588-4277-915b-84846c4ffe23)


2. Create subnets (public and private) in two availability zones
3. Create and attach Internet Gateway
4. Create NAT Gateway in a public subnet
5. Configure route tables for public and private subnets

### 2. Security Group Configuration
1. Create Load Balancer Security Group ![ELB security grp](https://github.com/user-attachments/assets/507f1e17-bafa-4748-9d7f-918fae4ee4e5)

2. Create Web Server Security Group
3. Create Bastion Host Security Group

### 3. Launch Bastion Host
1. Launch EC2 instance in public subnet using Bastion-SG![Bastian host](https://github.com/user-attachments/assets/e9bd1ff4-de2e-4562-98d9-fd00ef24c676)

2. Configure SSH access as per bastion_host_setup.md

### 4. Application Load Balancer Setup
1. Create Target Group
2. Create Application Load Balancer
3. Configure Listener![ed](https://github.com/user-attachments/assets/b7983848-8ee1-49a1-a89d-208f02deb79c)


### 5. Auto Scaling Group Configuration
1. Create Launch Template

aws ec2 create-launch-template --launch-template-name WebAppLaunchTemplate --version-description v1 --launch-template-data file://launch-template-data.json

2. Create Auto Scaling Group

aws autoscaling create-auto-scaling-group --auto-scaling-group-name WebAppASG --launch-template LaunchTemplateName=WebAppLaunchTemplate,Version='$Latest' --min-size 2 --max-size 4 --desired-capacity 2 --vpc-zone-identifier "subnet-xxxxx,subnet-yyyyy" --target-group-arns arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/WebAppTG/1234567890123456
![ASG](https://github.com/user-attachments/assets/e1ccd02c-1f29-49f1-a82d-af40af100391)

### 6. Application Deployment
1. SSH into instances via bastion host
2. Deploy your web application (e.g., using user data script)

### 7. Testing and Validation
1. Access application via Load Balancer DNS name![link](https://github.com/user-attachments/assets/d72df140-42bb-4c36-aede-a08b91eb143f)

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
