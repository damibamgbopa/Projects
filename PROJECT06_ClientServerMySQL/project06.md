# Dev Ops Training Projects - Darey.io  

# Understanding Client Server Architecture With MySQL As RDBMS

## Introduction

In both the physical and digital realms, the concept of client-server architecture is fundamental. Just as a client seeks a service and a server provides it, in the digital world, a client, such as a computer or mobile device, interacts with a server to access information or services. This interaction forms the basis of client-server architecture.

The diagram below illustrates a basic client-server architecture.

![](Images/simple_client_server.png)

In this architecture, clients, represented by computers or mobile devices, interact with a server to access services or retrieve information. The server, typically a remote computer, responds to client requests, facilitating the exchange of data or provision of services. This model forms the backbone of numerous digital systems, enabling seamless communication and resource sharing between clients and servers.


## Client-Server Architecture with MySQL

**MySQL**, developed by Oracle, is a relational database management system (RDBMS) that operates using structured query language (SQL). In client-server architecture with MySQL, MySQL serves both as a client and a server, facilitating data storage, retrieval, and management.

In previous projects, such as those involving LAMP and LEMP stacks, we adopted a client-server architecture where our web server acted as the client. In this setup, the web server would send requests to the database server to retrieve data and receive responses. This architecture, depicted in the image below, underscores the interaction between the web server and the database server, highlighting the flow of data retrieval in our system.

![](Images/mysql_cl_serv.png)

### Real Example of LAMP website

In Project03_LampStack we implemented a LAMP Stack website, we will once again look at an example of a commercially deployed LAMP Website -  `www.propitixhomes.com`

If we attempted to access this webpage from our website, our browser becomes the "Client", sending requests to the remote server and in turn expecting a response from the said server.

In the below image, our terminal is the client as we run the command - `curl -Iv www.propitixhomes.com` and the output below is the response from the server `www.propitixhomes.com`

![](<Images/01. propit_01.png>)

## Importance of Client-Server Architecture with MySQL

The significance of client-server systems with MySQL lies in their ability to efficiently and securely manage data for various applications. By separating user interfaces from data storage on servers, this architecture enables scalability, allowing multiple users to access the database concurrently. This structure ensures data integrity, security, and centralized storage, making it invaluable across industries like e-commerce and healthcare, where data management is critical.

## Project Prerequisites

1. **An AWS account/ Azure VM**
2. **Proficiency in Linux and its commands**
3. **SSH keys for secure access**


## Project Highlights

* **Understanding Client Server Architecture With MySQL As RDBMS**

* **Introduction**

 - Client Server Architecture With MySQL
 - Why Is A Client Server Architecture With MySQL Important
 - Project Prerequisites

* **Implementing MySQL As A Client Server Architecture**

 - Launching Azure VMs
 - Updating and Upgrading Package Lists
 - Installing MySQL Server Software
 - Insatlling MySQL Client Software
 - Allowing Access To MySQL Server For MySQL Client
 - Create A New User and Database On MySQL Server
 - Grant MySQL Client Administrative Prilvledges
 - Configure MySQL Server To Allow Connection From Remote Host
 - Connecting To MySQL Server From MySQL Client
 - Conclusion


## Implementing MySQL as a Client-Server Architecture

**Step 1: Launching Azure Virtual Machines**

- Create two instances on Azure: "mysql server" and "mysql client."
![](<Images/04. instances.png>)

- Access both instances via SSH.
![](<Images/02. ssh_client_server.png>)

**Step 2: Updating and Upgrading Package Lists** - `sudo apt update -y && sudo apt upgrade -y`
![](<Images/03. apt_update_upgrade.png>)

**Step 3: Installing MySQL Server Software** - `sudo apt install mysql-server -y`
Start MySQL service - `sudo systemctl start mysql`
Check MySQL status - `sudo systemctl status mysql`
![](<Images/05. status.png>)

**Step 4: Installing MySQL Client Software** - `sudo apt install mysql-client -y`
![](<Images/06. client_in.png>)

**Step 5: Allowing Access to MySQL Server for MySQL Client**
![](<Images/07. allow_inbound.png>)

**Step 6: Creating a New User and Database on MySQL Server**
In order for the MySQL client to be able to send requests to the MySQL server, we need to create a new user for the client on the server.

- Login to MySQL server - `sudo mysql` - ![](<Images/08. login_server.png>)
- Create a new user - `CREATE USER 'ubuntu'@'%' IDENTIFIED WITH mysql_native_password BY 'password';` - ![](<Images/09. new_user_ubuntu.png>)
- Create a database -  `CREATE DATABASE project_06;`
![](<Images/10. db_user.png>)

**Step 7: Granting MySQL Client Administrative Privileges**
- Grant Privileges - `GRANT ALL ON project_06.* TO 'ubuntu'@'%' WITH GRANT OPTION;`
![](<Images/11. grant_pri.png>)
- Exit MySQL and restart the service - `sudo systemctl restart mysql`
![](<Images/12. exit_restart.png>)

**Step 8: Configuring MySQL Server to Allow Remote Connections**

- Edit configuration file for MySQL server to allow remote connections - `sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf` - ![](<Images/13. bind_add.png>)


**Step 9: Connecting to MySQL Server from MySQL Client**
- Connect from client - `mysql -u ubuntu -h 172.210.11.45 -p project_06` - ![](<Images/14. connect_from_client.png>)
- Check connection state by querying databases - `show databases;` - ![](<Images/15. show_db.png>)

## Conclusion
In conclusion, understanding client-server architecture with MySQL is crucial for efficient data management and application development. By grasping this concept, professionals can enhance data accessibility, security, and scalability, thereby meeting the evolving demands of modern computing. This project provides practical insights into deploying and managing MySQL within a client-server environment, empowering individuals to build robust and effective systems.