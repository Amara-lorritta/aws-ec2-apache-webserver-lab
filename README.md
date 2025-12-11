## **AWS EC2 Web Server Lab**
EC2 • VPC • Networking • Web Server Configuration

This lab walks through the process of deleting and recreating a default VPC, launching an EC2 instance, configuring security groups, installing Apache, and publishing a simple HTML webpage. It demonstrates foundational AWS compute, networking, and web hosting concepts. 

## **Lab Overview**

This lab covers:

Recreating the AWS default VPC

Launching an EC2 instance with Amazon Linux 2023

Configuring SSH + HTTP security group rules

Installing and enabling Apache web server

Publishing a custom HTML page

Validating the web server using the instance’s public IPv4 address

## **Architecture Diagram**
<img width="611" height="421" alt="image" src="https://github.com/user-attachments/assets/8e74d34b-46b6-44cb-b785-17f1a41468da" />

## **Implementation Steps**
```bash
###########################################################
# 1. SIGN IN TO AWS MANAGEMENT CONSOLE
###########################################################
# - Click "Open Console" from the lab portal
# - Use provided IAM Username + Password
# - Do NOT edit the 12-digit Account ID
# - Set region: us-east-1 (N. Virginia)

###########################################################
# 2. DELETE AND RE-PROVISION THE DEFAULT VPC
###########################################################
# Console navigation:
#   VPC → Your VPCs
# Delete existing default VPC:
#   - Select VPC with "default VPC = yes"
#   - Actions → Delete VPC
#   - Check "I acknowledge" → type: default delete VPC → Delete

# Recreate default VPC:
#   - Refresh console
#   - Actions → Create Default VPC
#   - A new default VPC, subnets, IGW, route tables, and NACLs are provisioned automatically

###########################################################
# 3. LAUNCH EC2 INSTANCE (MyEC2Server)
###########################################################
# Console navigation:
#   EC2 → Instances → Launch Instances

# Instance settings:
#   - Name: MyEC2Server
#   - AMI: Amazon Linux 2023
#   - Instance type: t2.micro

# Create new key pair:
#   - Name: -----
#   - Type: RSA
#   - Format: .pem
#   - Download and save locally

# Network settings:
#   - Auto-assign Public IP: ENABLED
#   - Create new Security Group
#       SG Name: MyEC2Server_SG
#       Inbound rules:
#         SSH → Anywhere (0.0.0.0/0)
#         HTTP → Anywhere (0.0.0.0/0)

# Launch the instance:
#   - Click "Launch Instance"
#   - Go to Instances → wait for "Running" + "2/2 Checks Passed"
#   - Copy Public IPv4 address for later use

###########################################################
# 4. SSH INTO EC2 INSTANCE
###########################################################
# Console navigation:
#   - Select MyEC2Server
#   - Click "Connect"
#   - Choose "EC2 Instance Connect"
#   - Click "Connect"
# A browser terminal opens.

###########################################################
# 5. INSTALL AND START APACHE WEB SERVER
###########################################################
# Switch to root user:
sudo su

# Update packages:
dnf update -y

# Install Apache:
dnf install httpd -y

# Start Apache:
systemctl start httpd

# Enable persistence across reboots:
systemctl enable httpd

# Check status:
systemctl status httpd
# Should show: Active (running)

# Test in browser:
#   http://<Your_Public_IPv4_Address>/
# Apache test page should load.

###########################################################
# 6. CREATE AND PUBLISH A CUSTOM WEB PAGE
###########################################################
# Create index.html:
echo "<html>Hi Amarachi here, I am a public page, just trying to check if the web server is up and running</html>" > /var/www/html/index.html


# Restart Apache:
systemctl restart httpd

# Test in browser:
#   http://<Your_Public_IPv4_Address>/index.html
# Must use HTTP (not HTTPS)

# Expected output:
#   Hi Amarachi here, I am a public page, just trying to check if the web server is up and running.

###########################################################
# END OF LAB
###########################################################

```
## **Screnshoot**
<img width="1240" height="306" alt="Web server up and running" src="https://github.com/user-attachments/assets/d3396bc1-c80b-4099-b6fa-f4fc86aa03cb" />
<img width="1326" height="392" alt="web server running" src="https://github.com/user-attachments/assets/259aab8d-1969-496e-9d84-e76012540620" />

## **What Actually Happened**

When I deleted the default VPC, AWS automatically removed all of the networking components that come with it by default — including the internet gateway, the public and private subnets, the route tables, and the network ACLs. When I recreated the default VPC, AWS rebuilt all of these resources for me, giving me a clean, fully configured networking environment to launch my EC2 instance.

When I launched MyEC2Server, AWS provisioned an Amazon Linux 2023 t2.micro instance inside the public subnet of the new default VPC. Because I enabled Auto-Assign Public IP, and because the public subnet’s route table already routed outbound traffic to the Internet Gateway, my instance immediately became reachable from the internet.

The security group I created acted as a firewall for my instance, allowing SSH on port 22 and HTTP on port 80. This is why I was able to connect through EC2 Instance Connect and later access the Apache test page from my browser.

After switching to the root user, I installed Apache, started it, and enabled it as a persistent service. Once I created and published my own index.html file, my EC2 instance began serving that content publicly. Entering my instance’s public IPv4 address followed by /index.html displayed my custom webpage, confirming that the EC2 instance, the security group, the route table, and the internet gateway were all configured correctly.

## **Author**

Amarachi Emeziem
Cloud Support Engineer
