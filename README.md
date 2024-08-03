WordPress Website on AWS

This project demonstrates the deployment of a WordPress website on AWS using various AWS resources and services. The infrastructure is designed for high availability, scalability, and security.

Project Overview

The following AWS resources and services were utilized in this project:

Virtual Private Cloud (VPC): Configured with both public and private subnets across two different availability zones.
Internet Gateway: Facilitates connectivity between VPC instances and the wider Internet.
Security Groups: Acts as a network firewall mechanism.
Availability Zones: Leveraged two zones to enhance system reliability and fault tolerance.
Public Subnets: Used for infrastructure components like the NAT Gateway and Application Load Balancer.
EC2 Instance Connect Endpoint: Enables secure connections to assets within both public and private subnets.
Private Subnets: Web servers (EC2 instances) are positioned here for enhanced security.
NAT Gateway: Allows instances in both the private application and data subnets to access the Internet.
EC2 Instances: Hosted the WordPress website.
Application Load Balancer: Distributes web traffic evenly to an Auto Scaling Group of EC2 instances across multiple availability zones.
Auto Scaling Group: Automatically manages EC2 instances, ensuring website availability, scalability, fault tolerance, and elasticity.
GitHub: Used for version control and collaboration of web files.
Certificate Manager: Secures application communications.
Simple Notification Service (SNS): Configured to alert about activities within the Auto Scaling Group.
Route 53: Registered the domain name and set up a DNS record.
Elastic File System (EFS): Used for shared file system.
Relational Database Service (RDS): Used for the database.
Installation Scripts

Script to Install WordPress
bash
Copy code
# create to root user
sudo su

# update the software packages on the ec2 instance
sudo yum update -y

# create an html directory
sudo mkdir -p /var/www/html

# environment variable
EFS_DNS_NAME=fs-064e9505819af10a4.efs.us-east-1.amazonaws.com

# mount the efs to the html directory
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html

# install the apache web server, enable it to start on boot, and then start the server immediately
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# install php 8 along with several necessary extensions for wordpress to run
sudo dnf install -y \
php \
php-cli \
php-cgi \
php-curl \
php-mbstring \
php-gd \
php-mysqlnd \
php-gettext \
php-json \
php-xml \
php-fpm \
php-intl \
php-zip \
php-bcmath \
php-ctype \
php-fileinfo \
php-openssl \
php-pdo \
php-tokenizer

# install the mysql version 8 community repository
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm

# install the mysql server
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server

# start and enable the mysql server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html

# download wordpress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

# create the wp-config.php file
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# edit the wp-config.php file
sudo vi /var/www/html/wp-config.php

# restart the webserver
sudo service httpd restart
Script for Auto Scaling Group Launch Template
bash
Copy code
#!/bin/bash

# update the software packages on the ec2 instance
sudo yum update -y

# install the apache web server, enable it to start on boot, and then start the server immediately
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# install php 8 along with several necessary extensions for wordpress to run
sudo dnf install -y \
php \
php-cli \
php-cgi \
php-curl \
php-mbstring \
php-gd \
php-mysqlnd \
php-gettext \
php-json \
php-xml \
php-fpm \
php-intl \
php-zip \
php-bcmath \
php-ctype \
php-fileinfo \
php-openssl \
php-pdo \
php-tokenizer

# install the mysql version 8 community repository
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm

# install the mysql server
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server

# start and enable the mysql server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# environment variable
EFS_DNS_NAME=fs-02d3268559aa2a318.efs.us-east-1.amazonaws.com

# mount the efs to the html directory
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a

# set permissions
chown apache:apache -R /var/www/html

# restart the webserver
sudo service httpd restart
Repository

The reference diagram and scripts used to deploy the web app on an EC2 instance are available in the GitHub repository for this project.

