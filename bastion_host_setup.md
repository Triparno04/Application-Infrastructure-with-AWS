# Bastion Host Setup

## Overview
This document outlines the setup and configuration of the bastion host for secure access to our web application infrastructure.

## Bastion Host Specifications
- **Instance Type**: t2.micro
- **AMI**: Amazon Linux 2
- **Subnet**: Public Subnet
- **Security Group**: Bastion-SG

 ![Baston host Diagram](https://github.com/user-attachments/assets/53037b57-6947-4468-8e0f-447c97423030)


## Implementation Steps

1. Launch Bastion Host

aws ec2 run-instances --image-id ami-0123456789abcdef0 --count 1 --instance-type t2.micro --key-name YourKeyPair --security-group-ids sg-34567890 --subnet-id subnet-12345678 --associate-public-ip-address

2. Configure SSH Access
- Generate a new SSH key pair for the bastion host
- Add the public key to the `~/.ssh/authorized_keys` file on the bastion host

3. Configure SSH Client
Add the following to your SSH config file (`~/.ssh/config`):

Host bastion
HostName <Bastion-Public-IP>
User ec2-user
IdentityFile ~/.ssh/bastion-key.pem
Host webserver
HostName <WebServer-Private-IP>
User ec2-user
IdentityFile ~/.ssh/webserver-key.pem
ProxyJump bastion

4. Test Connection

ssh webserver

## Security Hardening
- Disable root login
- Use key-based authentication only
- Implement fail2ban for protection against brute force attacks
- Regularly update and patch the bastion host

## Monitoring and Logging
- Enable detailed CloudWatch monitoring
- Configure CloudWatch Logs for SSH access logs
