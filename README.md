# Secure Web Application Infrastructure on AWS

## Project Overview
This project demonstrates the implementation of a secure, scalable web application infrastructure on AWS using core services and best practices. It showcases a production-ready environment with emphasis on network security, high availability, and scalability.

## Architecture
![Architecture Diagram](architecture.png)

Key components:
- Amazon Virtual Private Cloud (VPC)
- Public and private subnets across two Availability Zones
- Internet Gateway and NAT Gateway
- Application Load Balancer
- EC2 instances in an Auto Scaling group
- Bastion host for secure SSH access
- Security Groups for access control

## Features
- **High Availability**: Multi-AZ deployment ensures fault tolerance
- **Scalability**: Auto Scaling group automatically adjusts capacity
- **Security**: Private subnets, bastion host, and security groups enhance protection
- **Load Balancing**: Efficient distribution of incoming traffic

## Technologies Used
- Amazon VPC
- Amazon EC2
- Amazon EC2 Auto Scaling
- Elastic Load Balancing (Application Load Balancer)
- Amazon CloudWatch

## Implementation Details
- [VPC Configuration](vpc_configuration.md)
- [EC2 and Auto Scaling Setup](ec2_autoscaling_setup.md)
- [Load Balancer Configuration](load_balancer_config.md)
- [Security Group Definitions](security_groups.md)
- [Bastion Host Setup](bastion_host_setup.md)

## Deployment Guide
For step-by-step deployment instructions, refer to the [Deployment Guide](deployment_guide.md).

## Future Improvements
- Implement Infrastructure as Code (IaC) using AWS CloudFormation or Terraform
- Integrate with CI/CD pipeline for automated deployments
- Implement AWS WAF for enhanced web application security
- Set up detailed CloudWatch alarms and custom metrics

## Key Learnings
- VPC architecture design for security and availability
- Load balancer and Auto Scaling configuration for high availability
- Bastion host implementation for secure access to private resources
- Fine-grained access control using Security Groups

## Screenshots
- [VPC Dashboard](https://github.com/user-attachments/assets/01a21cd0-85bd-446f-83a9-71acc7a1d66b) 
- [EC2 Instances](https://github.com/user-attachments/assets/527366ae-5be5-43b4-8abf-074a35cd6718)
- [Load Balancer Configuration](https://github.com/user-attachments/assets/00465617-34b1-45c8-b177-009ae4f964b0)
- [Auto Scaling Group](https://github.com/user-attachments/assets/370d58d5-1db4-432c-8d61-52c7d15f788b)


## Author
Triparno Bose

## License
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
