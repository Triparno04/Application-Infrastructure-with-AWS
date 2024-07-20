# Monitoring and Logging

## Overview
This document outlines the monitoring and logging setup for our web application infrastructure.

## CloudWatch Metrics

### VPC Flow Logs
- Enable VPC Flow Logs to capture information about IP traffic

aws ec2 create-flow-logs --resource-type VPC --resource-id vpc-0123456789abcdef0 --traffic-type ALL --log-destination-type cloud-watch-logs --log-group-name VPCFlowLogs

### EC2 Instances
- Monitor CPU Utilization, Network In/Out, Disk Read/Write Operations
- Set up detailed monitoring for more frequent metric collection

### Application Load Balancer
- Monitor RequestCount, TargetResponseTime, HTTPCode_ELB_4XX, HTTPCode_ELB_5XX

### Auto Scaling Group
- Monitor GroupMinSize, GroupMaxSize, GroupDesiredCapacity

## CloudWatch Alarms

1. High CPU Utilization

aws cloudwatch put-metric-alarm --alarm-name HighCPUUtilization --alarm-description "Alarm when CPU exceeds 70%" --metric-name CPUUtilization --namespace AWS/EC2 --statistic Average --period 300 --threshold 70 --comparison-operator GreaterThanThreshold --dimensions Name=AutoScalingGroupName,Value=WebAppASG --evaluation-periods 2 --alarm-actions arn:aws:sns:us-east-1:123456789012:AlertTopic

2. Unhealthy Host Count

aws cloudwatch put-metric-alarm --alarm-name UnhealthyHostCount --alarm-description "Alarm when unhealthy host count is greater than 0" --metric-name UnHealthyHostCount --namespace AWS/ApplicationELB --statistic Average --period 300 --threshold 0 --comparison-operator GreaterThanThreshold --dimensions Name=TargetGroup,Value=arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/WebAppTG/1234567890123456 --evaluation-periods 1 --alarm-actions arn:aws:sns:us-east-1:123456789012:AlertTopic

## CloudWatch Logs

### Web Server Logs
- Install and configure CloudWatch Logs agent on EC2 instances
- Configure log group and log stream for application logs

### Load Balancer Access Logs
- Enable access logs for Application Load Balancer

aws elbv2 modify-load-balancer-attributes --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/WebAppALB/1234567890123456 --attributes Key=access_logs.s3.enabled,Value=true Key=access_logs.s3.bucket,Value=my-alb-logs

## Custom Metrics
- Implement custom metrics for application-specific monitoring
- Use CloudWatch PutMetricData API to send custom metrics

## Dashboards
- Create CloudWatch dashboard for at-a-glance view of key metrics
aws cloudwatch put-dashboard --dashboard-name WebAppDashboard --dashboard-body file://dashboard-body.json

## Alerts and Notifications
- Set up SNS topics for different alert severities
- Configure alert actions for CloudWatch Alarms

## Best Practices
- Use metric filters to extract valuable information from logs
- Implement log retention policies to manage storage costs
- Regularly review and refine alarms and dashboards
- Use anomaly detection for proactive monitoring

