# VPC Configuration

## Overview
This document details the setup of the Virtual Private Cloud (VPC) for our secure web application infrastructure.

## VPC Specifications
- **CIDR Block**: 10.0.0.0/16
- **Region**: us-east-1
- **Availability Zones**: us-east-1a, us-east-1b

## Subnet Configuration
1. **Public Subnets**
   - us-east-1a: 10.0.1.0/24
   - us-east-1b: 10.0.2.0/24
   - Purpose: Hosting load balancers and bastion host

2. **Private Subnets**
   - us-east-1a: 10.0.3.0/24
   - us-east-1b: 10.0.4.0/24
   - Purpose: Hosting application servers

## Network Components
- **Internet Gateway**: Attached to VPC for public internet access
- **NAT Gateway**: Placed in public subnet for private subnet internet access
- **Route Tables**:
  - Public Route Table: Routes traffic to Internet Gateway
  - Private Route Table: Routes traffic to NAT Gateway

## Implementation Steps
1. Create VPC

aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=SecureWebAppVPC}]'

2. Create Subnets

aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.1.0/24 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.2.0/24 --availability-zone us-east-1b
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.3.0/24 --availability-zone us-east-1a
aws ec2 create-subnet --vpc-id <vpc-id> --cidr-block 10.0.4.0/24 --availability-zone us-east-1b

3. Create and Attach Internet Gateway

aws ec2 create-internet-gateway
aws ec2 attach-internet-gateway --vpc-id <vpc-id> --internet-gateway-id <igw-id>

4. Create NAT Gateway

aws ec2 create-nat-gateway --subnet-id <public-subnet-id> --allocation-id <elastic-ip-allocation-id>

5. Configure Route Tables

aws ec2 create-route-table --vpc-id <vpc-id>
aws ec2 create-route --route-table-id <public-rtb-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw-id>
aws ec2 create-route --route-table-id <private-rtb-id> --destination-cidr-block 0.0.0.0/0 --nat-gateway-id <nat-gw-id>

## Security Considerations
- Public subnets have direct internet access
- Private subnets access internet via NAT Gateway
- Network ACLs provide additional subnet-level security

## Monitoring
- Enable VPC Flow Logs for network traffic analysis
- Monitor NAT Gateway CloudWatch metrics for performance and cost optimization