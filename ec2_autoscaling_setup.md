# EC2 and Auto Scaling Setup

## Overview
This document outlines the configuration of EC2 instances and the Auto Scaling group for our web application.

## EC2 Instance Configuration
- **AMI**: Amazon Linux 2
- **Instance Type**: t2.micro (for demonstration purposes)
- **User Data**: Used for initial server configuration and application deployment

### User Data Script
```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from $(hostname -f)</h1>" > /var/www/html/index.html

Auto Scaling Group Configuration

Desired Capacity: 2
Minimum Size: 2
Maximum Size: 4
Health Check Type: ELB
Health Check Grace Period: 300 seconds

Launch Template

Name: WebAppLaunchTemplate
Instance Type: t2.micro
Security Group: WebAppSecurityGroup
IAM Instance Profile: WebAppInstanceProfile (if applicable)

Implementation Steps

Create Launch Template

aws ec2 create-launch-template --launch-template-name WebAppLaunchTemplate --version-description v1 --launch-template-data file://launch-template-data.json

Create Auto Scaling Group

aws autoscaling create-auto-scaling-group --auto-scaling-group-name WebAppASG --launch-template LaunchTemplateName=WebAppLaunchTemplate,Version='$Latest' --min-size 2 --max-size 4 --desired-capacity 2 --vpc-zone-identifier "subnet-xxxxx,subnet-yyyyy" --target-group-arns arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/WebAppTG/1234567890123456

Configure Scaling Policies

aws autoscaling put-scaling-policy --auto-scaling-group-name WebAppASG --policy-name WebAppScaleOutPolicy --policy-type SimpleScaling --adjustment-type ChangeInCapacity --scaling-adjustment 1 --cooldown 300

Monitoring and Maintenance

Set up CloudWatch alarms for CPU utilization to trigger scaling
Regularly update the Launch Template with the latest AMI
Monitor instance health and replace unhealthy instances

Security Considerations

Use IAM roles for EC2 instances instead of hardcoded credentials
Regularly patch and update instances
Implement proper security groups and network ACLs

Cost Optimization

Use Spot Instances for non-critical workloads
Implement automatic instance termination for cost savings during off-peak hours

load_balancer_config.md
```markdown
# Load Balancer Configuration

## Overview
This document details the setup and configuration of the Application Load Balancer (ALB) for our web application.

## Load Balancer Specifications
- **Type**: Application Load Balancer
- **Scheme**: Internet-facing
- **IP Address Type**: IPv4

## Listener Configuration
- **Protocol**: HTTP
- **Port**: 80

## Target Group Configuration
- **Name**: WebAppTargetGroup
- **Protocol**: HTTP
- **Port**: 80
- **Target Type**: Instance
- **Health Check Settings**:
  - Protocol: HTTP
  - Path: /
  - Interval: 30 seconds
  - Timeout: 5 seconds
  - Healthy Threshold: 2
  - Unhealthy Threshold: 2

## Implementation Steps
1. Create Target Group

aws elbv2 create-target-group --name WebAppTargetGroup --protocol HTTP --port 80 --vpc-id vpc-0123456789abcdef0 --health-check-protocol HTTP --health-check-path / --health-check-interval-seconds 30 --health-check-timeout-seconds 5 --healthy-threshold-count 2 --unhealthy-threshold-count 2

2. Create Load Balancer

aws elbv2 create-load-balancer --name WebAppALB --subnets subnet-12345678 subnet-87654321 --security-groups sg-12345678 --scheme internet-facing

3. Create Listener

aws elbv2 create-listener --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/WebAppALB/1234567890123456 --protocol HTTP --port 80 --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/WebAppTargetGroup/1234567890123456

## Security Considerations
- Configure SSL/TLS for HTTPS listeners
- Implement Web Application Firewall (WAF) rules
- Enable access logs for traffic analysis

## Monitoring and Maintenance
- Set up CloudWatch alarms for load balancer metrics
- Regularly review and update security groups
- Monitor target group health and troubleshoot unhealthy targets

## Performance Optimization
- Enable cross-zone load balancing for even distribution
- Implement sticky sessions if required by the application
- Configure connection draining for graceful instance deregistration

