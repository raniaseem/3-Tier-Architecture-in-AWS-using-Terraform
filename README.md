
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
