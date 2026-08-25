# Application Load Balancer Project

I completed an AWS project using two EC2 instances behind an Application Load Balancer.

The two web servers run Apache and return different messages so I can see which instance is receiving traffic.

## Setup

* 2 EC2 instances 
* Amazon Linux
* Apache
* Same VPC
* Different Availability Zones
* Application Load Balancer
* Target Group
* Security Groups
* HTTP on port 80

---

## Step 1 - Launch Two EC2 Instances

I launched two EC2 instances in the same VPC.

I placed the instances in different Availability Zones where possible.

### WebServer-1

I used the following User Data to install Apache and create a simple web page:

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello from WebServer-1</h1>" > /var/www/html/index.html
```

### WebServer-2

The second instance uses similar User Data but returns a different message:

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello from WebServer-2</h1>" > /var/www/html/index.html
```

The different messages make it easy to see which EC2 instance is receiving traffic.

<img width="1438" height="774" alt="aws_instancesrunning" src="https://github.com/user-attachments/assets/2085f1aa-f550-48f9-8a78-e97b4abb2981" />

---

## Step 2 - Create the ALB Security Group

I created a Security Group for the Application Load Balancer.

The ALB Security Group allows HTTP traffic from the internet.

This allows users to access the Application Load Balancer over HTTP.
<img width="1431" height="717" alt="aws_albsg" src="https://github.com/user-attachments/assets/d0e0ffc5-3db8-4217-bde8-0996427b180c" />

---

## Step 3 - Create the EC2 Security Group

I created a separate Security Group for the EC2 instances.

The EC2 Security Group only allows HTTP traffic from the ALB Security Group.

This keeps the EC2 instances from being directly accessible over HTTP from the internet.

<img width="1432" height="722" alt="aws_alb_ec2_albsg" src="https://github.com/user-attachments/assets/2f0a8b3e-dc41-41ce-9738-26a721025045" />

---

## Step 4 - Create a Target Group

I created an HTTP Target Group for the two EC2 instances.

The Target Group configuration was:

* Target type: Instances
* Protocol: HTTP
* Port: 80
* Health check protocol: HTTP
* Health check path: `/`

I then registered both EC2 instances with the Target Group.

---

## Step 5 - Create the Application Load Balancer

I created an **internet-facing Application Load Balancer**.

The ALB configuration was:

* Scheme: Internet-facing
* IP address type: IPv4
* Listener: HTTP
* Port: 80
* VPC: Same VPC as the EC2 instances
* Subnets: Two public subnets
* Security Group: ALB Security Group

I configured the HTTP listener to forward requests to the EC2 Target Group.

<img width="1437" height="718" alt="aws_alb" src="https://github.com/user-attachments/assets/1fc1ab0c-b847-49e3-a3a5-a5ae674ab3a7" />
<img width="1434" height="724" alt="aws_alb_2" src="https://github.com/user-attachments/assets/06b1a4a4-9d3a-418e-b062-890dd023f9ff" />

---

## Step 6 - Check Target Health

After creating the ALB, I checked the Target Group to make sure both EC2 instances were healthy.

The health check configuration was:

* Protocol: HTTP
* Port: 80
* Path: /

Both EC2 instances registered successfully and showed as healthy.

If an instance becomes unhealthy, the ALB will stop sending normal traffic to that instance.

<img width="1436" height="627" alt="aws_tg_healthy" src="https://github.com/user-attachments/assets/17e7271d-6b02-4ccf-a61d-523d173ef530" />

---

## Step 7 - Test the Application Load Balancer

I used the ALB DNS name to access the application in a web browser.

After refreshing the page, the request could be handled by the other EC2 instance.

This confirmed that the ALB was forwarding requests to the two EC2 instances.

<img width="1440" height="900" alt="aws_ec2server1" src="https://github.com/user-attachments/assets/7967c3c1-8c17-4417-b472-ccd9673699dd" />
<img width="1440" height="900" alt="aws_ec2server2" src="https://github.com/user-attachments/assets/e1c43a39-8123-4ddc-8446-2a91a5867a7e" />

---

## Step 8 - Verify the Security

Step 8 - Verify the Security

The final setup was tested to make sure that all internet traffic goes through the Application Load Balancer.

The ALB allows HTTP traffic from the internet, while the EC2 instances only allow HTTP traffic from the ALB Security Group.

This means users can access the website through the ALB, but they cannot access the EC2 instances directly from the internet.

This provides a more secure setup because the EC2 instances are protected behind the Application Load Balancer.

---

## Result

The project was successfully completed.

The final architecture included:

* Two EC2 web servers
* Different Availability Zones
* Apache web servers
* An Application Load Balancer
* An HTTP listener on port 80
* A Target Group
* Health checks
* ALB Security Group
* EC2 Security Group
* Traffic distribution between the two EC2 instances

The ALB successfully forwarded traffic to the healthy EC2 instances.

---

## What I Learned

* Setting up an Application Load Balancer
* Creating and configuring Target Groups
* Registering EC2 instances as targets
* Configuring health checks
* Using Security Groups between AWS resources
* Keeping EC2 instances from being directly exposed
* Testing load balancing across multiple instances

---

## AWS Services used

* Amazon EC2
* Application Load Balancer
* Target Groups
* Security Groups
* VPC
* Availability Zones

---

## Removing Resources

After testing, I removed the resources to avoid leaving anything running unnecessarily.

* Deleted the ALB
* Deleted the Target Group
* Terminated the EC2 instances
* Removed unused Security Groups
