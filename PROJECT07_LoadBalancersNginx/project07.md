# Dev Ops Training Projects - Darey.io  

# Implementing Load Balancers With Nginx

## Introduction

In the realm of web services, efficient distribution of tasks across multiple resources is crucial for maintaining performance and reliability. Load balancing, a technique employed by tech giants like Facebook, Netflix, and Amazon, plays a pivotal role in achieving this goal. In this project, we delve into the intricacies of load balancing using Nginx, a versatile web server capable of serving as a load balancer and reverse proxy.

## Concept of Load Balancing

Load balancing involves the equitable distribution of tasks among a cluster of servers, optimizing overall efficiency and resource utilization. It ensures that no single server becomes overloaded, thereby enhancing the responsiveness and availability of web services.

![](Images/load_balancer.jpeg)

## Importance of Load Balancing

In the contemporary landscape of IT and web services, load balancers are indispensable tools. They play a crucial role in ensuring the smooth operation and availability of applications, particularly in high-traffic scenarios. By evenly distributing network traffic across multiple servers, load balancers enhance performance, reliability, and scalability, thereby delivering an optimal user experience. Industries such as e-commerce, content delivery, and cloud computing rely heavily on load balancing to maintain robust online services.

## Project Prerequisites
- An AWS account
- Proficiency in Linux
- Basic understanding of web servers such as Apache and Nginx
- Familiarity with Linux text editors such as Vim and Nano

## Implementation Of Load Balancer with Nginx

### Step 1: Provisioning EC2 Instances

We start by provisioning three EC2 instances: two acting as web servers and one serving as a load balancer.

1. Launch two EC2 instances named "webserver_1" and "webserver_2".
2. Launch another EC2 instance named "load balancer".

![](<Images/01. ec2_instances.png>)

### Step 2: Open New Security Group For Both Webservers and Load Balancer

Configure security groups for both web servers and the load balancer, allowing traffic on specific ports.

1. Edit inbound rules to open port 8000 for both webserver_1 and webserver_2, and port 80 for the load balancer.
![](<Images/02. allow_port_8000.png>)
2. Allow traffic from anywhere on the specified ports.

### Step 3: Install Apache Webserver

Install and configure Apache on the web servers.

1. SSH into webserver_1 and webserver_2.
![](<Images/03. ssh_into_servers.png>)
2. Update and upgrade package lists - `sudo apt update -y && sudo apt upgrade -y`
![](<Images/04. update_upgrade.png>)
3. Install Apache - `sudo apt install apache2 -y`
4. Confirm successful installation of Apache - `sudo systemctl status apache2`
![](<Images/05. apache_successful_install.png>)

### Step 4: Configure Apache to Port 8000

Adjust Apache's configuration to listen on port 8000 to avoid port conflicts with the load balancer.

1. Edit the ports.conf file to add a new listen directive - `sudo nano /etc/apache2/ports.conf`
![](<Images/06. listen_8000.png>)
2. Adjust the virtualhost configuration to accommodate the new listen directive.
![](<Images/07. vh_statement.png>)
3. Reload Apache to apply the changes - `sudo systemctl reload apache2`

### Step 5: Configure Apache to Show Names Of Both Webservers

Modify the content served by Apache to display the names of the web servers.

1. Edit the index.html file in /var/www/html - `sudo nano /var/www/html/index.html`
![](<Images/08. index_htmL.png>)
2. Update the content of the file to display the IPs of the web servers 
![](<Images/09. ip_webserver.png>)
3. Save and close the file
4. Restart Apache to reflect the changes - `sudo systemctl restart apache2`
![](<Images/10. webserver1_ip.png>)
![](<Images/11. webserve2_ip.png>)

### Step 6: Install and Configure Nginx As A Load Balancer For Both WebServers

Install Nginx on the load balancer and configure it to distribute traffic to the web servers.

1. Update package lists and install Nginx - `sudo apt update -y && sudo apt install nginx -y`
2. Verify the successful installation of Nginx - `sudo systemctl status nginx`
![](<Images/12. nginx_install.png>)
3. Edit the Nginx load balancer configuration file - `sudo nano /etc/nginx/conf.d/loadbalancer.conf`
4. Define upstream backend servers and configure the server block to proxy_pass requests to these servers.
![](<Images/13. loadbalancer_conf.png>)
5. Test the Nginx configuration - `sudo nginx -t`
![](<Images/14. nginx_test.png>)
6. Restart Nginx to apply the changes - `sudo systemctl restart nginx`
7. Check Load Balancer IP in web browser for results
![](<Images/15. loadbalancer_web_1.png>)
![](<Images/16. loadbalancer_webtest_2.png>)

## Some Real Life Scenarios With Load Balancing

### Managing Workload Distribution for an Online Shopping Website

Imagine operating a popular online store that experiences heavy traffic during sales events. Load balancing with Nginx ensures that incoming requests are evenly distributed across multiple servers, preventing any single server from becoming overwhelmed. This approach optimizes resource utilization and enhances the shopping experience for customers.

### Ensuring Availability of a Content Delivery Network (CDN)

For a CDN delivering multimedia content worldwide, Nginx's load balancing capabilities are invaluable. By distributing content requests among servers located in different regions, Nginx ensures uninterrupted access to content. Additionally, Nginx's health checks detect and redirect traffic away from malfunctioning servers, maintaining seamless content delivery.

## Conclusion

Nginx's load balancing capabilities significantly enhance the performance, reliability, and scalability of web applications and services. By evenly distributing traffic across multiple servers, Nginx ensures optimal resource utilization and prevents server overload. Whether managing a busy online store, a global CDN, or a microservices architecture, Nginx's load balancing features are indispensable for optimizing system performance and delivering a seamless user experience in today's digital landscape.