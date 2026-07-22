
  #   AWS 3-Tier Architecture using Terraform
Overview

This project demonstrates deployment of a highly available and scalable 3-tier web application architecture on AWS using Terraform.

Technologies Used
AWS
Terraform
EC2
VPC
ALB
RDS MySQL
Auto Scaling
Security Groups
CloudWatch

# Architecture Diagram
<img width="867" height="519" alt="image" src="https://github.com/user-attachments/assets/07fe8a49-2f02-48b6-8ac7-0447d7c499d9" />


# Connect to Terraform
<img width="544" height="238" alt="image" src="https://github.com/user-attachments/assets/84445c0b-5c81-49fe-a627-17da3753b26a" />


# VPC Configuration
<img width="940" height="417" alt="image" src="https://github.com/user-attachments/assets/e343fe6b-32aa-4835-b0f5-b75678a3a6eb" />

# IAM role with EC2 Permission
<img width="940" height="418" alt="image" src="https://github.com/user-attachments/assets/cbbf3725-7a02-47f4-9f39-3366f15fc0d1" />

# RDS Database in 2 Availability Zones
<img width="940" height="419" alt="image" src="https://github.com/user-attachments/assets/5b5c5fa8-b717-4170-9ed3-5160fcdb1e8c" />

# Application Running
<img width="940" height="408" alt="image" src="https://github.com/user-attachments/assets/4985f1ec-be29-4e60-b328-5a7118966e2a" />

# Cloudwatch Monitoring
<img width="940" height="416" alt="image" src="https://github.com/user-attachments/assets/7b41287f-dac9-4dbc-b8ae-90467123dd9d" />

# Troubleshooting where i have face errors
# Troubleshooting Guide

## 1. Terraform Initialization Failed

### Error

```bash
Error: Failed to query available provider packages
```

### Solution

```bash
terraform init -upgrade
```

---

## 2. EC2 Instance Not Accessible

### Error

```bash
Connection timed out
```

### Checks

- Verify Security Group allows SSH (Port 22)
- Verify EC2 is running
- Verify correct key pair is used

```bash
ssh -i key.pem ec2-user@public-ip
```

---

## 3. ALB Returns 502 Bad Gateway

### Cause

Application service is not running on EC2.

### Verify

```bash
sudo systemctl status app
```

or

```bash
pm2 status
```

### Fix

```bash
pm2 restart all
```

---

## 4. Target Group Unhealthy

### Cause

Health check path is incorrect.

### Verify

```bash
curl localhost:3000
```

### Fix

Update ALB Health Check Path:

```text
/
```

or

```text
/health
```

---

## 5. RDS Connection Refused

### Error

```bash
connect ECONNREFUSED
```

### Checks

- RDS instance is available
- Security Group allows port 3306
- Database endpoint is correct

### Test

```bash
telnet <rds-endpoint> 3306
```

---

## 6. Access Denied for MySQL User

### Error

```bash
Access denied for user 'admin'
```

### Fix

Verify:

- Username
- Password
- Database permissions

```bash
mysql -h <endpoint> -u admin -p
```

---

## 7. Terraform State Lock Error

### Error

```bash
Error acquiring the state lock
```

### Fix

```bash
terraform force-unlock LOCK_ID
```

---

## 8. Auto Scaling Instances Not Launching

### Verify

- Launch Template exists
- AMI ID is valid
- Security Group is attached

### Check

```bash
aws autoscaling describe-auto-scaling-groups
```

---

## 9. Application Not Loading Through ALB

### Verify

```bash
curl localhost:3000
```

Application should return:

```text
AWS 3 Tier Architecture Application Running
```

---

## Lessons Learned

- Network Security Groups are critical.
- ALB Health Checks must match application routes.
- RDS should remain private.
- Terraform state management is important.
- Proper monitoring reduces downtime.

# Cost Optimization

This project follows AWS cost optimization best practices to reduce unnecessary cloud spending while maintaining performance and availability.

## Cost Optimization Strategies

### 1. Right-Sized EC2 Instances

- Used t2.micro / t3.micro instances for development environments.
- Avoided over-provisioning compute resources.
- Auto Scaling automatically adjusts capacity based on demand.

### 2. Auto Scaling

- Launches new instances only when required.
- Terminates unused instances during low traffic periods.
- Reduces infrastructure costs while maintaining availability.

### 3. Private RDS Deployment

- Database deployed in private subnets.
- Prevents unnecessary public exposure.
- Improves security without additional infrastructure costs.

### 4. Terraform Infrastructure as Code

- Prevents resource duplication.
- Enables quick teardown of unused environments.

```bash
terraform destroy
```

### 5. Resource Tagging

Resources are tagged for better cost tracking.

Example:

```hcl
tags = {
  Environment = "Dev"
  Project     = "AWS-3Tier-Architecture"
}
```

### 6. Monitoring with CloudWatch

- Monitor CPU Utilization
- Monitor Memory Usage
- Monitor Application Health

Helps identify underutilized resources for optimization.

### 7. Backup Management

- Automated RDS backups configured.
- Retention period adjusted according to business requirements.
- Old snapshots removed to reduce storage costs.

### 8. Security Group Optimization

- Restricted network access.
- Reduced unnecessary data transfer and exposure risks.

## Estimated Monthly Cost (Free Tier Eligible)

| Service | Estimated Cost |
|----------|---------------|
| VPC | Free |
| Security Groups | Free |
| EC2 t2.micro | Free Tier Eligible |
| Application Load Balancer | Low Cost |
| RDS MySQL | Free Tier Eligible |
| CloudWatch | Basic Monitoring Free |

> Note: Actual cost depends on traffic, storage, backup retention, and region.

## Cost Monitoring

AWS services used for monitoring spending:

- AWS Cost Explorer
- AWS Budgets
- CloudWatch Metrics

## Lessons Learned

- Always delete unused resources.
- Remove unattached EBS volumes.
- Delete old snapshots.
- Stop idle EC2 instances.
- Use Auto Scaling instead of permanently running large instances.
- Monitor AWS costs regularly using Cost Explorer and Budgets.
