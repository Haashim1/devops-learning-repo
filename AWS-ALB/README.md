# Application Load Balancer Project

I built an AWS project using two EC2 instances behind an Application Load Balancer.

The two web servers run Apache and return different messages so I can see which instance is receiving traffic.

## Setup

* 2 × EC2 instances 
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


### WebServer-2

The second instance uses similar User Data but returns a different message:

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello from WebServer-2</h1>" > /var/www/html/index.html


The different messages make it easy to see which EC2 instance is receiving traffic.

<img width="1437" height="721" alt="aws_instancesrunning" src="https://github.com/user-attachments/assets/6d5712ca-4f75-48ca-a33e-67015ec8a578" />


---

## Step 2 - Create the ALB Security Group

I created a Security Group for the Application Load Balancer.

The ALB Security Group allows HTTP traffic from the internet.

| Type | Port | Source      |
| ---- | ---: | ----------- |
| HTTP |   80 | `0.0.0.0/0` |

This allows users to access the Application Load Balancer over HTTP.

<img width="1431" height="717" alt="aws_albsg" src="https://github.com/user-attachments/assets/6bf72655-af59-454f-bc39-20295146a01f" />

---

## Step 3 - Create the EC2 Security Group

I created a separate Security Group for the EC2 instances.

The EC2 Security Group only allows HTTP traffic from the ALB Security Group.

| Type | Port | Source             |
| ---- | ---: | ------------------ |
| HTTP |   80 | ALB Security Group |

This keeps the EC2 instances from being directly accessible over HTTP from the internet.

<img width="1432" height="722" alt="aws_alb_ec2_albsg" src="https://github.com/user-attachments/assets/1a4fc2af-7172-4f75-8167-6c5f71ed2a1f" />

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

---

## Step 6 - Check Target Health

After creating the ALB, I checked the Target Group to make sure both EC2 instances were healthy.

The health check configuration was:

```text
Protocol: HTTP
Port: 80
Path: /
```

Both EC2 instances registered successfully and showed as **Healthy**.

If an instance becomes unhealthy, the ALB will stop sending normal traffic to that instance.

<img width="1440" height="900" alt="aws_tg_healthy" src="https://github.com/user-attachments/assets/2ab20f82-dd60-4192-a66f-a3f45f103b4d" />

---

## Step 7 - Test the Application Load Balancer

I used the ALB DNS name to access the application in a web browser.

The response could show:

```text
Hello from WebServer-1
```

or:

```text
Hello from WebServer-2
```

After refreshing the page, the request could be handled by the other EC2 instance.

This confirmed that the ALB was forwarding requests to the two EC2 instances.

<img width="1440" height="900" alt="aws_ec2server1" src="https://github.com/user-attachments/assets/8951e376-69de-4db8-a5d4-0c9d72ceb0f6" />
<img width="1440" height="900" alt="aws_ec2server2" src="https://github.com/user-attachments/assets/162ca21a-d13b-4107-9729-d08eb3545c99" />

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

## AWS Services

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
