# Dev Ops Training Projects - Darey.io 

# LEMP STACK IMPLEMENTATION
![](Images/LEMP_Stack.webp)

## Introduction to LEMP Stack

In the previous project, we explored the LAMP Stack, and now it's time to delve into the LEMP Stack. Understanding this stack is crucial for web development, as it comprises Linux, Nginx, MySQL, and PHP, offering a powerful combination for hosting web applications.

### What is LEMP Stack?

The LEMP Stack is a collection of open-source software that works seamlessly together to host web applications. Unlike the LAMP Stack, which uses Apache, the LEMP Stack utilizes Nginx as its web server. This difference brings its own set of advantages, particularly in terms of performance and scalability.

### Advantages of LEMP Stack

1. **Performance**: Nginx, the core of the LEMP Stack, is renowned for its high performance and ability to handle multiple connections simultaneously. This is crucial for applications that need to support heavy traffic.
  
2. **Security**: Properly configuring the LEMP Stack is essential for ensuring the security of web applications. This knowledge is invaluable for DevOps engineers and system administrators in safeguarding against common web vulnerabilities and attacks.

3. **Database Management**: MySQL, a key component of the LEMP Stack, provides robust database management capabilities. Understanding how to set up and manage databases is vital for applications requiring data storage and retrieval.

4. **Server-side Functionality**: PHP, integrated into the LEMP Stack, enables server-side functionality in web applications. This allows developers to create dynamic and interactive websites that respond to user interactions.


## Prerequisites

- An AWS account with an Ubuntu EC2 instance.
![](<Images/01. prerequisite_aws.png>)
- Access to a terminal or virtual machine.
![](<Images/02. connect_machine.png>)
- Knowledge of SSH for connecting to the EC2 instance.


# Project Highlights

- Introduction to LEMP Stack
- Getting Started with LEMP Stack
- Conclusion


## Getting Started with LEMP Stack

Let's dive into the practical implementation of the LEMP Stack.

### Step 0: Launch an Ubuntu Instance on AWS Console and SSH Into It

Follow the instructions outlined in the previous project for launching an Ubuntu instance and connecting to it via SSH.
![](<Images/03. ssh_ubuntu.png>)

### Step 1: Installing Nginx

**Begin by updating the package lists and apt repositories, then install Nginx web server. Don't forget to allow firewall access for Nginx and SSH.**

- `sudo apt update` - ![](<Images/04. install_nginx1.png>)
- `sudo apt install nginx` - ![](<Images/05. install_nginx2.png>)
- `sudo systemctl status nginx` - ![](<Images/06. status_nginx.png>)
- Enabling inbound connection through port 80 - ![](<Images/07. inbound_conn.png>)
- Access using curl - `curl http://localhost:80` - ![](<Images/08. Access_using_curl.png>)
- Access from Internet - ![](<Images/09. web_interface.png>)

### Step 2: Installing MySQL

**Next, install MySQL as the database management system for storing application data. Secure the MySQL installation and verify its status.**

- `sudo apt install mysql-server` - ![](<Images/10. install_mysql.png>)
- `sudo mysql` - ![](<Images/11. sudo_mysql.png>)
- Run script to remove insecure default settings and lock down access to DBS, but first, set a password for the root user -
`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';` - ![](<Images/12. alter_pwd.png>)
- Exit MySQL - ![](<Images/13. exit_mysql.png>)
- Interactive script - `sudo mysql_secure_installation` - ![](<Images/14. validate_password.png>)
- Login to MySQL using PW - `sudo mysql -p` - ![](<Images/15. login_pw_change.png>)
- Exit MySQL - ![](<Images/13. exit_mysql.png>)

### Step 3: Installing PHP

**Install PHP, enabling server-side functionality for web applications. PHP-FPM is used to integrate PHP with Nginx.**

- `sudo apt install php-fpm php-mysql` - ![](<Images/16. install_php.png>)

### Step 4: Configuring Nginx Web Server to Serve as a Virtual Host

**Configure Nginx to serve web applications by creating a virtual host. This involves setting up directories, creating server block configurations, and enabling PHP processing.**

- Create the root web directory for ProjectLEMP - `sudo mkdir /var/www/html/projectLEMP`
- Create simple HTML file which Nginx will serve - `sudo nano /var/www/projectLEMP/index.html`, it's content should be 
"<h1>Welcone to Darey.io, ProjectLEMP Nginx works</h1>"
- Assign Ownership of Directory - `sudo chown -R $USER:$USER /var/www/html/projectLEMP`
- Open config file - `sudo nano /etc/nginx/sites-available/projectLEMP`
- Create a link - `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
![](<Images/17. nginx.png>)
![](<Images/18. code_nginx.png>)
- Unlink default config - `sudo rm /etc/nginx/sites-enabled/default` or `sudo unlink /etc/nginx/sites-enabled/default`
- Reload nginx config file - `sudo nginx -t` - ![](<Images/19. reload_nginx.png>)
- Reload Nginx to apply changes - `sudo systemctl reload nginx`
![](<Images/20. nginx_web.png>)


### Step 5: Testing PHP with Nginx

**Create a simple PHP file to test PHP processing with Nginx. This ensures that PHP is properly integrated and functioning within the LEMP stack.**

- Create PHP Test file - `nano /var/www/projectLEMP/info.php` - ![](<Images/21. php_test_file.png>)
- Web View - ![](<Images/22. web_php_info.png>)
- Remove info.php - `sudo rm /var/www/projectLEMP/info.php`

### Step 6: Retrieving data from MySQL database with PHP

Connect PHP with MySQL to retrieve and display data from the database. This step demonstrates the full functionality of the LEMP stack in serving dynamic web content.


## Conclusion

The LEMP Stack offers a robust and versatile solution for hosting web applications. By mastering its components and configurations, learners can enhance their skills in web development, system administration, and database management. Continuously practice and explore further to solidify your understanding and apply the LEMP Stack to diverse projects and scenarios.