<h1 align="center">🌩️ AWS CloudFormation – VPC, EC2, and Application Load Balancer (Lab 6)</h1>

<p align="center">
  <img src="https://img.shields.io/badge/AWS-CloudFormation-orange?logo=amazon-aws&logoColor=white" alt="AWS CloudFormation">
  <img src="https://img.shields.io/badge/Service-EC2%20%7C%20VPC%20%7C%20ALB-blue?logo=amazon-aws&logoColor=white" alt="AWS Services">
  <img src="https://img.shields.io/badge/Region-ca--central--1-success" alt="AWS Region">
  <img src="https://img.shields.io/badge/Language-JSON-lightgrey" alt="Template Language">
</p>

---

## 🏗️ Overview

This project automates the deployment of a **complete AWS environment** using **CloudFormation**.  
It provisions a **VPC**, **public/private subnets**, **EC2 web servers**, and an **Application Load Balancer (ALB)** across two Availability Zones in **Canada (Central)**.

---

## 🧭 Architecture Diagram

```mermaid
flowchart TB
    A[🌐 Internet] --> B[ALB - Application Load Balancer]
    B --> C1[EC2 Instance 1<br>Public Subnet (AZ1a)]
    B --> C2[EC2 Instance 2<br>Public Subnet (AZ1b)]
    subgraph VPC["VPC (10.0.0.0/16)"]
        subgraph PublicSubnets["Public Subnets"]
            C1
            C2
        end
        subgraph PrivateSubnets["Private Subnets"]
            D1[Private Subnet 1 (AZ1a)]
            D2[Private Subnet 2 (AZ1b)]
        end
    end
🧩 The ALB distributes HTTP traffic evenly between two EC2 instances in separate Availability Zones.

🌎 Region and Availability Zones
Resource	Availability Zone	CIDR Block
Public Subnet 1	ca-central-1a	10.0.1.0/24
Private Subnet 1	ca-central-1a	10.0.2.0/24
Public Subnet 2	ca-central-1b	10.0.3.0/24
Private Subnet 2	ca-central-1b	10.0.4.0/24

Region: ca-central-1 🇨🇦 (Canada Central)

⚙️ EC2 Configuration
Property	Value
AMI	ami-0f3c8a41d58224188 (Amazon Linux 2023 – x86_64)
Instance Type	t2.micro (Free Tier eligible)
Key Pair	canada-lab6-key
User Data Script	Installs and starts Apache web server with a custom HTML page

bash
Copy code
#!/bin/bash
yum update -y
yum install -y httpd
systemctl enable httpd
systemctl start httpd
echo "<html><h1>Welcome to HTTP Server</h1></html>" > /var/www/html/index.html
Each instance serves a different page (“Server 1” / “Server 2”) for load-balancing verification.

☁️ CloudFormation Stack Details
🧩 Parameters
KeyName → EC2 key pair name

InstanceAMI → Amazon Linux 2023 AMI ID

SecurityGroupDescription → Description for EC2 Security Group

🏗️ Resources
VPC, Subnets, Route Tables, and Internet Gateway

Security Groups (for EC2 + ALB)

Two EC2 Instances (Apache web servers)

Application Load Balancer, Listener, and Target Group

📤 Outputs
LoadBalancerDNSName → URL to access the web app

VPCId → ID of the created VPC

🚀 Deployment Steps
Open AWS Console → CloudFormation → Create Stack → With new resources (standard)

Upload → lab6-vpc-alb-ec2.json

Parameters

KeyName: select canada-lab6-key

Keep all defaults

Click Next → Next → Create Stack

Wait for status → ✅ CREATE_COMPLETE

Go to Outputs → copy the LoadBalancerDNSName

Open in your browser → refresh to see both web servers responding 🎉

🔍 Verification
🧪 Browser Test
bash
Copy code
http://<LoadBalancerDNSName>
Displays alternating responses:

“Welcome to HTTP Server 1 (ca-central-1a)”

“Welcome to HTTP Server 2 (ca-central-1b)”

💻 CLI Test
bash
Copy code
curl http://<LoadBalancerDNSName>
🔐 SSH Access
bash
Copy code
ssh -i canada-lab6-key.pem ec2-user@<EC2-Public-IP>
Username: ec2-user

💡 Future Enhancements
Add a NAT Gateway for private subnet internet access

Restrict EC2 inbound HTTP to only ALB Security Group

Introduce an Auto Scaling Group for high availability

Parameterize subnet CIDRs and AZs

Add RDS or S3 resources in private subnets

📘 Summary
Component	Count	Description
VPC	1	Main networking boundary
Subnets	4	2 Public + 2 Private across 2 AZs
EC2 Instances	2	Apache web servers
Application Load Balancer	1	Distributes HTTP traffic
Security Groups	2	For ALB and EC2 isolation
Outputs	2	ALB DNS + VPC ID

👨‍💻 Author
Saravanan Nadanasabesan
🎓 Durham College – Cloud Computing Program
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
This confirms that both EC2 instances are healthy and traffic is being distributed by the ALB ✅
