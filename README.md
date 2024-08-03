# WordPress Hosting on AWS

I recently completed a DevOps project where I hosted a WordPress website on AWS. This README provides an overview of the project's architecture, configuration, and deployment steps. The reference diagram and deployment scripts are available in this [GitHub repository](URL).

## Project Overview

The project involved setting up a highly available and secure WordPress website using various AWS services. The key components and configurations are summarized below.

## Architecture and Configuration

1. **Virtual Private Cloud (VPC) Setup:**
   - Configured a VPC with both public and private subnets across two different Availability Zones for high availability and fault tolerance.
   - Deployed an Internet Gateway to facilitate connectivity between VPC instances and the wider Internet.
   - Established Security Groups to act as a network firewall mechanism for controlling inbound and outbound traffic.

2. **Networking and Connectivity:**
   - Utilized Public Subnets for infrastructure components like the NAT Gateway and Application Load Balancer.
   - Implemented EC2 Instance Connect Endpoint for secure connections to assets within both public and private subnets.
   - Enabled instances in both the private Application and Data subnets to access the Internet via the NAT Gateway.

3. **Web Server and Load Balancing:**
   - Hosted the WordPress website on EC2 instances within Private Subnets for enhanced security.
   - Employed an Application Load Balancer and a target group for evenly distributing web traffic to an Auto Scaling Group of EC2 instances across multiple Availability Zones.
   - Utilized an Auto Scaling Group to automatically manage EC2 instances, ensuring website availability, scalability, fault tolerance, and elasticity.

4. **Additional Configurations:**
   - Stored web files on GitHub for version control and collaboration.
   - Secured application communications using AWS Certificate Manager.
   - Configured Simple Notification Service (SNS) to alert about activities within the Auto Scaling Group.
   - Registered the domain name and set up a DNS record using Route 53.
   - Used Elastic File System (EFS) for shared file storage.
   - Used Relational Database Service (RDS) for database management.

