# Application Load Balancer Project

Deploy two EC2 web servers behind an AWS Application Load Balancer and use Security Groups to control traffic between the ALB and EC2 instances.

---

# Step 1 - Launch Two EC2 Instances

I launched two EC2 instances in the same VPC.

I used:

* Instance 1: WebServer-1
* Instance 2: WebServer-2
* Instance type: t3.micro
* Amazon Linux
* Apache web server
* Different Availability Zones where possible

Each server displays different content so I can identify which EC2 instance is handling the request.

### WebServer-1

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello from WebServer-1</h1>" > /var/www/html/index.html
```

### WebServer-2

```bash
#!/bin/bash

dnf update -y
dnf install -y httpd

systemctl enable httpd
systemctl start httpd

echo "<h1>Hello from WebServer-2</h1>" > /var/www/html/index.html
```


<img width="1437" height="721" alt="aws_instancesrunning" src="https://github.com/user-attachments/assets/9f341944-662a-4b12-8d59-ecb0f07b2397" />


---

# Step 2 - Create the ALB Security Group

I created a Security Group for the Application Load Balancer.

The inbound rule allows HTTP traffic from the internet.

| Type | Port | Source      |
| ---- | ---: | ----------- |
| HTTP |   80 | `0.0.0.0/0` |


<img width="1431" height="717" alt="aws_albsg" src="https://github.com/user-attachments/assets/0858ec7d-d76a-4207-86d4-8a299b2ee50e" />


---

# Step 3 - Create the EC2 Security Group

I created a separate Security Group for the EC2 instances.

The EC2 instances do not allow HTTP traffic directly from the internet.

The inbound rule allows HTTP traffic only from the ALB Security Group.

| Type | Port | Source             |
| ---- | ---: | ------------------ |
| HTTP |   80 | ALB Security Group |

The traffic flow is:

```text
Internet → ALB → EC2
```

Instead of:

```text
Internet → EC2
```

<img width="1432" height="722" alt="aws_alb_ec2_albsg" src="https://github.com/user-attachments/assets/b7f9ee0d-6503-4ae9-b710-256c1baddf6d" />


---

# Step 4 - Create a Target Group

I created a Target Group for the two EC2 instances.

I used:

* Target type: Instances
* Protocol: HTTP
* Port: 80
* Health check protocol: HTTP
* Health check path: `/`

I then registered both EC2 instances with the Target Group.

```text
Target Group
     |
     +---- WebServer-1
     |
     +---- WebServer-2
```

---

# Step 5 - Create the Application Load Balancer

I created an internet-facing Application Load Balancer.

I used:

* Scheme: Internet-facing
* IP address type: IPv4
* Listener: HTTP
* Port: 80
* Same VPC as the EC2 instances
* Two public subnets
* ALB Security Group

I configured the listener to forward traffic to my Target Group.

```text
Internet
    |
    | HTTP :80
    v
Application Load Balancer
    |
    v
Target Group
   / \
  /   \
 EC2  EC2
```


<img width="1440" height="900" alt="aws_alb" src="https://github.com/user-attachments/assets/68df25eb-ed8b-4a54-b6aa-e0975a30bd1d" />

---

# Step 6 - Check Target Health

After creating the ALB, I checked the Target Group.

Both EC2 instances were registered as targets.

The health check uses:

```text
/
```

Both instances should show:

```text
Healthy
```

<img width="1440" height="900" alt="aws_tg_healthy" src="https://github.com/user-attachments/assets/46f32e43-ac00-4551-8cad-49b39bd64506" />


---

# Step 7 - Test the Application Load Balancer

I copied the DNS name provided by AWS for the Application Load Balancer and opened it in my browser.

Web-ALB-676051393.eu-west-2.elb.amazonaws.com 

The ALB successfully forwarded traffic to my EC2 instances.

The first response showed:

```text
Hello from WebServer-1
```

After refreshing the page, I could receive:

```text
Hello from WebServer-2
```

This confirmed that the Application Load Balancer was distributing traffic between the two EC2 instances.

### Screenshot

<img width="1440" height="900" alt="aws_ec2server1" src="https://github.com/user-attachments/assets/c35a3e14-f71c-4e66-88da-3cda78dc63da" />
<img width="1440" height="900" alt="aws_ec2server2" src="https://github.com/user-attachments/assets/87efcc23-03df-4de4-9148-64dfeb0e7334" />


---

# Step 8 - Verify Security

The final traffic flow was:

```text
Internet
    |
    v
Application Load Balancer
    |
    v
Target Group
   / \
  /   \
 EC2  EC2
```

The ALB Security Group allows HTTP traffic from the internet.

The EC2 Security Group only allows HTTP traffic from the ALB Security Group.

This means:

```text
Internet → ALB → EC2
```

is allowed.

But:

```text
Internet → EC2
```

is not allowed.

---

# Final Result

The project was successfully completed.

I created:

* Two EC2 web servers
* EC2 instances in different Availability Zones
* An Application Load Balancer
* A Target Group
* HTTP listener on port 80
* Health checks
* ALB Security Group
* EC2 Security Group

The ALB successfully distributed traffic between both EC2 instances.

---

# What I Learned

This project helped me understand how an Application Load Balancer works with multiple EC2 instances.

I learned how to:

* Create an Application Load Balancer
* Create a Target Group
* Register EC2 instances as targets
* Configure health checks
* Use Security Groups to control traffic
* Prevent direct access to EC2 instances
* Test traffic distribution through the ALB

---

# AWS Services Used

* Amazon EC2
* Application Load Balancer
* Target Groups
* Security Groups
* VPC
* Availability Zones

---

# Removing resources

After completing the project, I removed the AWS resources to avoid unnecessary charges.

* Deleted the Application Load Balancer
* Deleted the Target Group
* Terminated the EC2 instances
* Removed unused Security Groups
