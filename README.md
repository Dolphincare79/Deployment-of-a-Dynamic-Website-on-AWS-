# 🛠️ Dynamic Website Deployment on AWS – DevOps Project

## 📘 Project Overview

This project demonstrates the deployment of a dynamic PHP-based website on AWS using a highly available, scalable, and secure architecture. It leverages multiple AWS services to ensure fault tolerance, elasticity, and secure access to resources across public and private subnets.

## 📌 Architecture Highlights

- **VPC Configuration**: Custom VPC with public and private subnets across two Availability Zones.
- **Internet Gateway**: Enables internet access for public-facing resources.
- **Security Groups**: Configured to control inbound/outbound traffic to EC2 instances and other services.
- **High Availability**: Resources distributed across two Availability Zones for fault tolerance.
- **Public Subnets**: Host NAT Gateway and Application Load Balancer.
- **Private Subnets**: Host EC2 web servers and database instances for enhanced security.
- **EC2 Instance Connect Endpoint**: Secure access to instances in both subnet types.
- **NAT Gateway**: Allows private subnet instances to access the internet securely.
- **Application Load Balancer**: Distributes traffic to EC2 instances in an Auto Scaling Group.
- **Auto Scaling Group**: Ensures scalability and availability of EC2 instances.
- **AWS Certificate Manager**: Secures application traffic with SSL/TLS.
- **SNS Notifications**: Alerts for Auto Scaling Group activities.
- **Route 53**: Domain registration and DNS configuration.
- **S3 Bucket**: Stores application code and assets.

## 🚀 Deployment Script

The EC2 user data script automates the setup of the web server, PHP environment, MySQL database, and deployment of the application code from S3.

```bash
#!/bin/bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl enable httpd 
sudo systemctl start httpd

sudo dnf install -y php php-pdo php-openssl php-mbstring php-exif php-fileinfo php-xml php-ctype php-json php-tokenizer php-curl php-cli php-fpm php-mysqlnd php-bcmath php-gd php-cgi php-gettext php-intl php-zip

sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server 
sudo systemctl start mysqld
sudo systemctl enable mysqld

sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

S3_BUCKET_NAME=seuncare-shopwise-web-files
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html
cd /var/www/html
sudo unzip shopwise.zip
sudo cp -R shopwise/. /var/www/html/
sudo rm -rf shopwise shopwise.zip
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/
sudo vi .env
sudo service httpd restart
```

## 📁 Repository Contents

- `reference-diagram.png`: Visual representation of the AWS architecture.
- `deployment-script.txt`: EC2 user data script for automated setup.
- `README.md`: Project documentation.

## 🌐 Domain & DNS

The website is accessible via a custom domain registered and managed through **Amazon Route 53**, with DNS records pointing to the Application Load Balancer.

## 📣 Notifications

AWS **SNS** is configured to send alerts for scaling events and other activities within the Auto Scaling Group.

## 📦 Application Code

The application code is stored in an S3 bucket and deployed to EC2 instances during initialization.
