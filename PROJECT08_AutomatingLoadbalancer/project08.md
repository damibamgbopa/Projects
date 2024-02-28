# Dev Ops Training Projects - Darey.io  

# Automating Loadbalancer Configuration With Shell scripting

## Introduction

In today's fast-paced technological landscape, automation stands as a cornerstone in the realm of DevOps engineering. Automation streamlines processes, reduces manual intervention, and enhances efficiency across various tasks. As Larry Wall aptly put it, "A good engineer is a lazy one," emphasizing the ethos of seeking ways to automate tasks to improve efficacy.

## What Is Automation?

Automation is the practice of leveraging modern technologies to eliminate manual processes. From mundane, repetitive tasks to complex decision-making processes, automation plays a pivotal role in accelerating workflows and reducing the likelihood of errors. For DevOps engineers, automation lies at the heart of their work, facilitating rapid deployment of services while minimizing operational risks.
![](Images/automation.webp)

## Importance Of Automation

In the intricate web of technology and industry, automating load balancer configuration with tools like Nginx emerges as a critical necessity. Load balancers, such as Nginx, effectively manage web traffic distribution, ensuring smooth and efficient operation of web applications. In an era where web services experience surging user bases, automation becomes indispensable in deploying and scaling applications seamlessly.

The significance of automating load balancer configuration extends across various industries, including online shopping, content delivery, and microservices. By automating load balancer setup and maintenance, organizations can save valuable time and resources while bolstering the reliability and scalability of their web services. Thus, mastering the automation of load balancing with Nginx becomes imperative for DevOps engineers striving to meet the evolving demands of the digital landscape.

## Project Prerequisites
- An AWS account
- Proficiency in Linux
- Previous Knowledge of Shell Scripting
- Basic understanding of web servers such as Apache and Nginx
- Familiarity with Linux text editors such as Vim and Nano


# Deploying And Configuring Webservers and Load Balancer

## Step 1: Launching EC2 Instances

We will provision three EC2 instances: two for our webservers and one for the load balancer.
1. Launch two EC2 instances and name them "webserver_1" and "webserver_2"
2. Launch another EC2 instance and name it "load balancer"
![](<Images/01. ec2_instances.png>)


## Step 2: Setting Up Security Groups
1. For the webservers and load balancer, navigate to the security groups.
2. Edit inbound rules to open port 8000 for both webserver_1 and webserver_2 and port 80 for our load balancer.
3. Allow traffic from anywhere on the open ports.
![](<Images/02. allow_port_8000.png>)
4. SSH into the three instances.
![](<Images/03. ssh_3.png>)

## Step 3: Automating Webservers Configuration With Shell Script
1. Create the script file - `touch install.sh`
1. Paste the following code into the script file on webserver 1 and 2 instances to configure each web server - `sudo nano install.sh`
    #!/bin/bash

    ####################################################################################################################
    ##### This automates the installation and configuring of apache webserver to listen on port 8000
    ##### Usage: Call the script and pass in the Public_IP of your EC2 instance as the first argument as shown below:
    ######## ./install.sh 54.224.67.155
    ####################################################################################################################

    set -x # debug mode
    set -e # exit the script if there is an error
    set -o pipefail # exit the script when there is a pipe failure

    PUBLIC_IP=$54.224.67.155   # place the public ip address of each webserver

    [ -z "${PUBLIC_IP}" ] && echo "Please pass the public IP of your EC2 instance as an argument to the script" && exit 1

    sudo apt update -y &&  sudo apt install apache2 -y

    sudo systemctl status apache2

    if [[ $? -eq 0 ]]; then
        sudo chmod 777 /etc/apache2/ports.conf
        echo "Listen 8000" >> /etc/apache2/ports.conf
        sudo chmod 777 -R /etc/apache2/

        sudo sed -i 's/<VirtualHost \*:80>/<VirtualHost *:8000>/' /etc/apache2/sites-available/000-default.conf

    fi
    sudo chmod 777 -R /var/www/
    echo "<!DOCTYPE html>
           <html>
           <head>
               <title>My EC2 Instance</title>
           </head>
           <body>
               <h1>Welcome to my EC2 instance</h1>
               <p>Public IP: "${PUBLIC_IP}"</p>
           </body>
           </html>" > /var/www/html/index.html

    sudo systemctl restart apache2

![](<Images/04. script_oo.png>)

**Note:** Replace "PUBLIC_IP=$1" with the public IP address of each web server.

### Explanation of the Shell Script:

1. Shebang: The script begins with a shebang line #!/bin/bash, indicating that it is a Bash shell script.
2. Comments: Throughout the script, comments are provided to offer explanations and usage instructions for each section of the code.
3. Debug Mode: The set -x command enables debug mode, printing each command to the console before execution. This aids in troubleshooting and understanding the script's flow.
4. Exit on Error: With set -e, the script exits immediately if any command returns a non-zero exit status, indicating an error.
5. Exit on Pipe Failure: set -o pipefail ensures that the script exits if any command within a pipeline fails, preventing unexpected behavior.
6. User Input: The script expects the public IP address of the EC2 instance as a command-line argument, assigning it to the PUBLIC_IP variable.
7. Argument Check: The script verifies if the PUBLIC_IP variable is empty and displays an error message along with usage instructions if it is. This ensures proper usage of the script and prevents execution errors.
8. Update and Install Apache: The script begins by updating the package list with sudo apt update -y and then proceeds to install the Apache web server with sudo apt install apache2 -y.
9. Check Apache Status: It verifies the status of the Apache service using sudo systemctl status apache2. If the service is running successfully (return code 0), the script continues with the configuration process.
10. Configuration Changes: The script applies the following configuration changes to Apache:
   - It grants full read and write permissions to /etc/apache2/ports.conf to facilitate editing of the configuration file.
   - Appends Listen 8000 to /etc/apache2/ports.conf, directing Apache to listen on port 8000.
   - Grants full read and write permissions recursively to the /etc/apache2/ directory.
   - Modifies the <VirtualHost *:80> line in /etc/apache2/sites-available/000-default.conf to <VirtualHost *:8000>, ensuring Apache listens on port 8000.
11. Set Up Web Page: The script provides full read and write permissions recursively to /var/www/ directory and creates an HTML file (index.html) in /var/www/html/ directory. This HTML file displays a basic web page containing the instance's public IP address.
12. Restart Apache: Finally, the script restarts the Apache service with sudo systemctl restart apache2 to apply the configuration changes and ensure they take effect.

2. Save and run the script - `sudo chmod +x install.sh`, `./install.sh 54.198.24.71`, `./install.sh 54.224.67.155`
![](<Images/05. run_script.png>)
![](<Images/07. webserver_1.png>)
![](<Images/06. webserver_2.png>)


## Step 4: Automating Load Balancer Configuration With Shell Script
On our load balancer instance;

1. Create the script file - `touch nginx_loadbalancer.sh`
2. Open a file and paste the following shell script:

#!/bin/bash

    ######################################################################################################################
    ##### This automates the configuration of Nginx to act as a load balancer
    ##### Usage: The script is called with 3 command line arguments. The public IP of the EC2 instance where Nginx is installed
    ##### The webserver urls for which the load balancer distributes traffic. An example of how to call the script is shown below:
    ##### ./nginx_loadbalancer.sh PUBLIC_IP Webserver-1 Webserver-2
    #####  ./nginx_loadbalancer.sh 18.215.236.69 54.198.24.71 54.224.67.155
    ############################################################################################################# 

PUBLIC_IP=$18.215.236.69
firstWebserver=$54.198.24.71:8000
secondWebserver=$54.224.67.155:8000

[ -z "${PUBLIC_IP}" ] && echo "Please pass the Public IP of your EC2 instance as the argument to the script" && exit 1

[ -z "${firstWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the second argument to the script" && exit 1

[ -z "${secondWebserver}" ] && echo "Please pass the Public IP together with its port number in this format: 127.0.0.1:8000 as the third argument to the script" && exit 1

set -x # debug mode
set -e # exit the script if there is an error
set -o pipefail # exit the script when there is a pipe failure


sudo apt update -y && sudo apt install nginx -y
sudo systemctl status nginx

if [[ $? -eq 0 ]]; then
    sudo touch /etc/nginx/conf.d/loadbalancer.conf

    sudo chmod 777 /etc/nginx/conf.d/loadbalancer.conf
    sudo chmod 777 -R /etc/nginx/

    
    echo " upstream backend_servers {

            # You are to replace the public IP and Port to that of your webservers
            server  54.198.24.71:8000; # public IP and port for webserser 1
            server  54.224.67.155:8000; # public IP and port for webserver 2

            }

           server {
            listen 80;
            server_name 18.215.236.69;

            location / {
                proxy_pass http://backend_servers;   
            }
    } " > /etc/nginx/conf.d/loadbalancer.conf
fi

sudo nginx -t

sudo systemctl restart nginx



![](<Images/08. nginx_loadbalancer.png>)

3. Save and close the file.
4. Make the script executable and run it - `sudo chmod +x nginx_loadbalancer.sh`, `./nginx_loadbalancer.sh 18.215.236.69 54.198.24.71 54.224.67.155`
![](<Images/09. run_scrip.png>)

![](<Images/10. lb_1.png>)
![](<Images/11. lb_2.png>)

## Conclusion

This project equips learners with essential skills in automation, particularly in automating load balancer configuration using shell scripting. By leveraging Nginx's load balancing capabilities, DevOps engineers can enhance the performance, stability, and scalability of web applications. With practical knowledge gained from this project, professionals are better equipped to build robust and reliable web services, ultimately improving user experience and ensuring business continuity.