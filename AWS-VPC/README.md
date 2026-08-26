# AWS VPC & Networking Project

I built a custom AWS network from scratch using Amazon VPC.

The goal of this project was to understand the fundamentals of:

* VPC networking
* Public and private subnets
* Internet Gateways
* NAT Gateways
* Route tables
* EC2 networking
* Security Groups

---

## Architecture

The final setup consisted of:

* **1 custom VPC**
* **1 public subnet**
* **1 private subnet**
* **1 Internet Gateway**
* **1 NAT Gateway**
* **1 Elastic IP**
* **1 public route table**
* **1 private route table**
* **1 public EC2 instance**
* **1 private EC2 instance**
* **Security Groups** 

### Network Architecture

```text
                         INTERNET
                             |
                             |
                     Internet Gateway
                             |
                     +-------+-------+
                     |               |
               Public Subnet    NAT Gateway
                     |               |
                 Public EC2           |
                                     |
                              Private Route Table
                                     |
                              Private Subnet
                                     |
                                 Private EC2
```

---

# Step 1 - Create the VPC

I created a custom VPC using the CIDR block:

```text
10.0.0.0/16
```

I then created two subnets:

| Subnet         | Type    | CIDR          |
| -------------- | ------- | ------------- |
| Public Subnet  | Public  | `10.0.1.0/24` |
| Private Subnet | Private | `10.0.2.0/24` |

The public subnet is used for resources that need direct internet access.

The private subnet is used for resources that should not be directly accessible from the internet.

<img width="1386" height="676" alt="aws1_vpc" src="https://github.com/user-attachments/assets/ebcda392-9a04-46f9-9316-595234094d35" />


---

# Step 2 - Create Internet Gateway

I created an Internet Gateway named:

```text
lab-igw
```

I then attached the Internet Gateway to my custom VPC.

The Internet Gateway allows resources in the public subnet to communicate with the internet when the correct routing and public IP configuration are in place.

<img width="1435" height="754" alt="01-internet-gateway-attached" src="https://github.com/user-attachments/assets/2890cfb9-7382-4966-a3bc-5a9a71478766" />


The screenshot shows the Internet Gateway `lab-igw` in an attached state.

---

# Step 3 - Create Elastic IP and NAT Gateway

I allocated an Elastic IP address for the NAT Gateway.

I then created a NAT Gateway inside the public subnet.

The NAT Gateway allows instances in the private subnet to make outbound internet connections without giving those instances public IP addresses.

<img width="1440" height="707" alt="02-nat-gateway-available" src="https://github.com/user-attachments/assets/14857537-6204-4aec-97c1-d98b55c2d05d" />

---

# Step 4 - Configure Route Tables

I created two route tables:

* Public Route Table
* Private Route Table

## Public Route Table

The public route table was associated with the public subnet.

I added the following default route:

```text
0.0.0.0/0 → Internet Gateway
```

This allows resources in the public subnet to access the internet.

## Private Route Table

The private route table was associated with the private subnet.

I added:

```text
0.0.0.0/0 → NAT Gateway
```

This allows resources in the private subnet to access the internet through the NAT Gateway.

The private EC2 instance does not need a public IP address.

<img width="1429" height="773" alt="04-ec2-instances-running" src="https://github.com/user-attachments/assets/38089bfe-b44e-45ed-b92e-e3d40393ef82" />

---

# Step 5 - Launch Public EC2 Instance

I launched an EC2 instance inside the public subnet.

The instance was configured with:

* Public subnet
* Public IPv4 address
* Public EC2 Security Group
* SSH access restricted to my IP address
* HTTP access restricted according to the assignment requirements

The public instance can communicate with the internet through the Internet Gateway.

---

# Step 6 - Launch Private EC2 Instance

I launched a second EC2 instance inside the private subnet.

The private instance was configured without a public IPv4 address.

It can communicate with the internet for outbound connections through the following path:

```text
Private EC2
     ↓
Private Route Table
     ↓
NAT Gateway
     ↓
Internet Gateway
     ↓
Internet
```

The private EC2 instance cannot be directly accessed from the public internet.

---

# Step 7 - Configure Security Groups

I configured Security Groups to control network access between the EC2 instances and the internet.

## Public EC2 Security Group

The public EC2 Security Group allows:

* SSH (port 22)** from my IP address
* HTTP (port 80) as required

SSH access was restricted to my own IP rather than allowing access from anywhere.

## Private EC2 Security Group

The private EC2 Security Group only allows internal access.

For example:

```text
Public EC2 Security Group
            ↓
     Private EC2
```

This prevents the private instance from being directly accessed from the public internet.
<img width="1440" height="747" alt="07-public-sg-inbound-rules" src="https://github.com/user-attachments/assets/474d52b3-0d78-41ad-ab20-b80b9796abcf" />

---

# Step 8 - Test the Network

I tested the network configuration to make sure the routing and security controls worked correctly.

## Public EC2

The public EC2 instance was able to communicate with the internet through the **Internet Gateway**.

## Private EC2

The private EC2 instance did not have a public IP address but was able to make outbound internet connections through the NAT Gateway.

This confirmed that the private subnet was correctly isolated while still having controlled outbound internet access.

---

# What I Learned

I learned how AWS networking components work together to create a secure cloud network.

Key concepts I practiced:

* Creating a custom VPC
* Designing public and private subnets
* Configuring Internet Gateways
* Configuring NAT Gateways
* Using Elastic IP addresses
* Creating and associating route tables
* Deploying EC2 instances into different subnets
* Configuring Security Groups
* Separating public-facing and private resources
* Controlling internet access through routing

---

# Result

The final environment successfully separated public and private resources while providing controlled internet access.

The public EC2 instance could access the internet directly through the Internet Gateway, while the private EC2 instance could access the internet through the NAT Gateway without requiring a public IP address.

This demonstrated how AWS VPC networking can be used to create a network that provides both internet connectivity and resource isolation**.

---

# AWS Services Used

* Amazon VPC
* Amazon EC2
* Internet Gateway
* NAT Gateway
* Elastic IP
* Route Tables
* Security Groups
