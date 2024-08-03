# Hosting-a-WordPress-Site-on-AWS

WordPress on AWS

This project demonstrates how to host a WordPress website on AWS using various AWS resources and services. The deployment ensures high availability, security, and scalability. The reference diagram and deployment scripts are available in this GitHub repository.

Architecture Overview

Key Components
VPC Configuration:
Virtual Private Cloud (VPC) with both public and private subnets across two Availability Zones.
Internet Gateway for connectivity between VPC instances and the Internet.
Security:
Security Groups as network firewall mechanisms.
EC2 Instance Connect Endpoint for secure connections to assets within both public and private subnets.
Infrastructure:
Public Subnets for components like NAT Gateway and Application Load Balancer.
Private Subnets for web servers (EC2 instances) for enhanced security.
High Availability and Scalability:
Two Availability Zones for system reliability and fault tolerance.
Application Load Balancer and a target group for evenly distributing web traffic.
Auto Scaling Group to manage EC2 instances automatically.
Storage and DNS:
EFS for shared file system.
RDS for database.
Domain name and DNS setup using Route 53.
Monitoring and Notifications:
Simple Notification Service (SNS) for alerts about activities within the Auto Scaling Group.
Deployment Steps

Step 1: VPC Configuration
Configure a VPC with both public and private subnets across two Availability Zones, and deploy an Internet Gateway.

Step 2: Security Groups
Establish Security Groups to act as network firewalls.

Step 3: Subnets
Utilize Public Subnets for NAT Gateway and Application Load Balancer. Position web servers in Private Subnets.

Step 4: EC2 Instances
Deploy EC2 instances in Private Subnets. Use an Auto Scaling Group to manage these instances.

Step 5: Load Balancer
Implement an Application Load Balancer to distribute web traffic to the EC2 instances.

Step 6: Storage
Use EFS for a shared file system and RDS for the database.

Step 7: DNS and SSL
Register a domain and set up DNS records using Route 53. Secure communications using AWS Certificate Manager.

Step 8: Monitoring
Configure SNS to alert about activities within the Auto Scaling Group.

Scripts

WordPress Installation Script
bash
Copy code
# Switch to root user
sudo su

# Update the software packages
sudo yum update -y

# Create the HTML directory
sudo mkdir -p /var/www/html

# Set environment variable
EFS_DNS_NAME=fs-064e9505819af10a4.efs.us-east-1.amazonaws.com

# Mount the EFS to the HTML directory
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html

# Install Apache web server, enable it to start on boot, and start the server
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP 8 and necessary extensions
sudo dnf install -y php php-cli php-cgi php-curl php-mbstring php-gd php-mysqlnd php-gettext php-json php-xml php-fpm php-intl php-zip php-bcmath php-ctype php-fileinfo php-openssl php-pdo php-tokenizer

# Install MySQL version 8 community repository and server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server

# Start and enable the MySQL server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html

# Download WordPress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

# Create the wp-config.php file
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# Edit the wp-config.php file
sudo vi /var/www/html/wp-config.php

# Restart the web server
sudo service httpd restart
Auto Scaling Group Launch Template Script
bash
Copy code
#!/bin/bash

# Update the software packages
sudo yum update -y

# Install Apache web server, enable it to start on boot, and start the server
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP 8 and necessary extensions
sudo dnf install -y php php-cli php-cgi php-curl php-mbstring php-gd php-mysqlnd php-gettext php-json php-xml php-fpm php-intl php-zip php-bcmath php-ctype php-fileinfo php-openssl php-pdo php-tokenizer

# Install MySQL version 8 community repository and server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server

# Start and enable the MySQL server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Set environment variable
EFS_DNS_NAME=fs-02d3268559aa2a318.efs.us-east-1.amazonaws.com

# Mount the EFS to the HTML directory
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a

# Set permissions
chown apache:apache -R /var/www/html

# Restart the web server
sudo service httpd restart
Conclusion

This project showcases how to deploy a WordPress website on AWS with a focus on high availability, security, and scalability. The provided scripts and architecture diagram offer a practical guide for setting up a similar environment.
