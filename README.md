# 🌩️ AWS CloudFormation – VPC, EC2, and Application Load Balancer (Lab 6)

This project automates the deployment of a complete AWS environment using **CloudFormation**.  
It provisions a **VPC**, **public/private subnets**, **EC2 web servers**, and an **Application Load Balancer (ALB)** across multiple Availability Zones in the **Canada (Central)** region.

---

## 🏗️ Architecture Overview

### 🔹 Components Created

| Category | Resources |
|-----------|------------|
| **Networking** | VPC (10.0.0.0/16), Internet Gateway, 4 Subnets (2 Public + 2 Private), 2 Route Tables |
| **Compute** | 2 × EC2 Instances (`t2.micro`, Amazon Linux 2023) |
| **Load Balancing** | Application Load Balancer (ALB) with Target Group + Listener (HTTP/80) |
| **Security** | ALB SG (HTTP/80), EC2 SG (HTTP/80 + SSH/22) |
| **Outputs** | ALB DNS Name, VPC ID |

---

## 🌎 Region and Availability Zones

| Resource Type | Availability Zone | CIDR Block |
|----------------|------------------|-------------|
| Public Subnet 1 | ca-central-1a | 10.0.1.0/24 |
| Private Subnet 1 | ca-central-1a | 10.0.2.0/24 |
| Public Subnet 2 | ca-central-1b | 10.0.3.0/24 |
| Private Subnet 2 | ca-central-1b | 10.0.4.0/24 |

> 🧭 **Region:** Canada (Central) – `ca-central-1`

---

## ⚙️ EC2 Configuration

| Property | Value |
|-----------|--------|
| **AMI** | `ami-0f3c8a41d58224188` (Amazon Linux 2023 – x86_64) |
| **Instance Type** | `t2.micro` |
| **Key Pair** | `canada-lab6-key` |
| **User Data Script** | Installs and starts Apache web server with a custom HTML page |

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl enable httpd
systemctl start httpd
echo "<html><h1>Welcome to HTTP Server</h1></html>" > /var/www/html/index.html
Each instance displays its own page (Server 1 or Server 2) for load-balancing verification.

☁️ CloudFormation Stack Details
🧩 Parameters
KeyName → EC2 key pair name

InstanceAMI → Amazon Linux 2023 AMI ID

SecurityGroupDescription → Description for EC2 security group

🏗️ Resources
VPC, Subnets, Route Tables, and Internet Gateway

Security Groups (for EC2 + ALB)

Two EC2 Instances (web servers)

Application Load Balancer, Listener, and Target Group

📤 Outputs
LoadBalancerDNSName – URL to access the web app

VPCId – ID of the created VPC

🚀 Deployment Steps
Open AWS Console → CloudFormation → Create Stack → With new resources (standard)

Choose Upload a template file → upload lab6-vpc-alb-ec2.json

Under Parameters:

KeyName: select your EC2 key pair (e.g., canada-lab6-key)

Leave other values as default

Click Next → Next → Create Stack

Wait until status shows CREATE_COMPLETE

Go to Outputs → copy LoadBalancerDNSName

Open the DNS link in your browser → you should see both servers alternating 🎉

🔍 Verification
✅ Browser Test
bash
Copy code
http://<LoadBalancerDNSName>
Displays alternating responses from Server 1 and Server 2.

✅ Command Line Test
bash
Copy code
curl http://<LoadBalancerDNSName>
✅ SSH (optional)
bash
Copy code
ssh -i canada-lab6-key.pem ec2-user@<EC2-Public-IP>
Username: ec2-user

💡 Enhancement Ideas
Add a NAT Gateway to enable outbound internet access for private subnets

Restrict EC2 Security Group (allow HTTP only from ALB SG)

Add Auto Scaling Group for dynamic scaling

Parameterize subnet CIDRs and AZs

Deploy RDS or S3 resources within private subnets

📘 Summary
Component	Count	Purpose
VPC	1	Networking boundary
Subnets	4	Public & Private across two AZs
EC2	2	Apache web servers
ALB	1	Load balances HTTP traffic
Security Groups	2	Isolate ALB and EC2 access
Outputs	2	ALB DNS Name & VPC ID

👨‍💻 Author
Saravanan Nadanasabesan
📍 Durham College – Cloud Computing Program
🗓️ Lab 6: AWS CloudFormation Automation
🌐 Region: ca-central-1 (Canada Central)

🏁 Final Output
After successful deployment, visiting the Load Balancer DNS Name displays:

pgsql
Copy code
Welcome to HTTP Server 1 (ca-central-1a)
or

pgsql
Copy code
Welcome to HTTP Server 2 (ca-central-1b)
This confirms that both EC2 instances are healthy and traffic is load balanced via the ALB ✅
