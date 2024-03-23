# Implementing Wordpress Website With LVM Storage Management

## Introduction:
Welcome to a project where we delve deep into the world of WordPress websites, coupled with state-of-the-art storage management using Logical Volume Management (LVM). WordPress reigns as a powerhouse in content management, while LVM revolutionizes storage handling in Linux systems. Buckle up as we merge these technologies for unparalleled scalability, resilience, and maintenance ease.

## The Power of LVM Storage:
Logical Volume Management (LVM), your ticket to dynamic storage management in Linux environments. It's not just about disks; it's about unleashing flexibility and efficiency in storage space utilization. Join us on this journey to harness the full potential of LVM within the WordPress ecosystem.

![](Images/Datacenter-Server.jpg)

## Importance:
Leveraging LVM within WordPress isn't just about staying ahead; it's about redefining the rules. By optimizing resource usage and ensuring data integrity, we pave the way for seamless expansion and unmatched performance. This project isn't just for the tech-savvy; it's for those ready to transform the digital landscape.


## Project Prerequisites:
* Basic knowledge of Linux systems.
* Understanding of web development concepts.
* Familiarity with WordPress.
* Basic understanding of MySQL

### Implementing Your WordPress Web Solution:
Picture a three-tiered structure—your client, your web server (hosting WordPress), and your database server—all seamlessly connected for a smooth sailing experience.
![](Images/Three-Tier-architecture.png)

**Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.**
The 3-Tier Setup
* A Laptop or PC to serve as a client
* An EC2 Linux Server as a web server (This is where you will install WordPress)
* An EC2 Linux server as a database (DB) serve

1. Create an AWS Instance using RedHat Distribution - Web Server (This will serve as the web server, create 3 volumes in the same Availability Zone (AZ) as the web server.)
![](<Images/01. wbserver_rh.png>)

2. Attach three volumes one by one to the Webserver instance.
![](<Images/02. volumes.png>)
![](<Images/03. attach_volumes.png>)

3. Let's begin the configuration - Use `lsblk` to inspect what block devices are attached to the server.
![](<Images/04. lsblk.png>)

All devices in Linux reside in `/dev/` directory. Inspect it with `ls /dev/` and make sure you see all 3 newly created block devices there – their names will likely be `xvdf`, `xvdh`, `xvdg`.
![](<Images/05. ls_dev.png>)

4. Use `df -h` command to see all mounts and free space on your server
![](<Images/06. df_h.png>)

5. Use gdisk utility to create a single partition on each of the 3 disks
    * `sudo gdisk /dev/xvdf` - ![](<Images/07. vol1.png>)
    * `sudo gdisk /dev/xvdg` - ![](<Images/07. vol2.png>)
    * `sudo gdisk /dev/xvdh` - ![](<Images/07. vol3.png>)

6. Use `lsblk` utility to view the newly configured partition on each of the 3 disks.
![](<Images/08. lsblk_part.png>)

7. Install lvm2 package using `sudo yum install lvm2`. Run `sudo lvmdiskscan` command to check for available partitions.
![](<Images/09. install_lvm2.png>), ![](<Images/09. lvmdiskscan.png>)

8. Use `sudo pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.
```
    sudo pvcreate /dev/xvdf1
    sudo pvcreate /dev/xvdg1
    sudo pvcreate /dev/xvdh1
```
![](<Images/10. sudo_pvcreate.png>)
 
Verify that the physical volume has been created using `sudo pvs` - ![](<Images/11. sudo_pvs.png>)

9. Use `sudo vgcreate` utility to add all 3 PVs to a volume group (VG). Let the Volume Group be named `webdata-vg`
`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1` - ![](<Images/12. vgcreate.png>)

10. Verify that the volume group has been created using `sudo vgs` - ![](<Images/13. vgs.png>)

11. Use `sudo lvcreate` utility to create 2 logical volumes. 
    `apps-lv` (Use half of the PV size), and `logs-lv` Use the remaining space of the PV size. 
    **NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.**
```
    sudo lvcreate -n apps-lv -L 59G webdata-vg
    sudo lvcreate -n logs-lv -L 59G webdata-vg
```
![](<Images/14. lvcreate.png>)

12. Verify that your Logical Volume has been created successfully by running `sudo lvs` -![](<Images/15. sudo_lvs.png>)

13. Verify the entire setup by running `sudo vgdisplay -v`
![](<Images/16. setup_display1.png>)
![](<Images/16. setup_display2.png>)

14. Use `mkfs.ext4` to format the logical volumes with `ext4` filesystem
```
    sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
    sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
![](<Images/17. mk_format.png>)

15. Create `/var/www/html` directory to store website files - `sudo mkdir -p /var/www/html`
16. Create `/home/recovery/logs` to store backup of log data -`sudo mkdir -p /home/recovery/logs`
17. Mount `/var/www/html` on apps-lv logical volume - `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`
18. Use `rsync` utility to backup all the files in the log directory `/var/log into /home/recovery/logs` (This is required before mounting the file system) - 
`sudo rsync -av /var/log/. /home/recovery/logs/`
19. Mount `/var/log` on logs-lv logical volume. 
(**Note that all the existing data on /var/log will be deleted. That is why step 14 above is very important**) - `sudo mount /dev/webdata-vg/logs-lv /var/log`
20. Restore log files back into /var/log directory -`sudo rsync -av /home/recovery/logs/. /var/log`
![](<Images/18. adjust_1.png>)
21. Update `/etc/fstab` file so that the mount configuration will persist after restart of the server
    The UUID of the device will be used to update the /etc/fstab file;
    `sudo blkid`
    `sudo vi /etc/fstab`

    9d18da20-f053-48f0-9151-0f0cb24aec34 - logs
    9b68296f-5c49-4252-9e60-2915e93b09e0 - apps
![](<Images/19. blkid.png>)
![](<Images/20. sudo_vi_fstab.png>)

Test the configuration and reload the daemon
```
    sudo mount -a
    sudo systemctl daemon-reload
```
Verify your setup by running `df -h`, output should look like this - ![](<Images/21. df_h.png>)


## Prepare the Database Server
1. Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
**Note: Repeat the same steps as for the Web Server, but instead of apps-lv and logs-lv, create db-lv and logs-lv, then mount it to /db directory instead of /var/www/html/.**
![](<Images/22. db_server.png>)

2. Attach three volumes one by one to the DB Server instance.
![](<Images/23. vol456.png>)

3. Use `lsblk` to inspect what block devices are attached to the server.
![](<Images/24. lsblk.png>)

All devices in Linux reside in `/dev/` directory. Inspect it with `ls /dev/` and make sure you see all 3 newly created block devices there – their names will likely be `xvdf`, `xvdh`, `xvdg`.
![](<Images/25. ls_dev.png>)

4. Use `df -h` command to see all mounts and free space on your DB server
![](<Images/26. df_h.png>)

5. Use gdisk utility to create a single partition on each of the 3 disks
    * `sudo gdisk /dev/xvdf` - ![](<Images/27. vol4.png>)
    * `sudo gdisk /dev/xvdg` - ![](<Images/27. vol5.png>)
    * `sudo gdisk /dev/xvdh` - ![](<Images/27. vol6.png>)

6. Use `lsblk` utility to view the newly configured partition on each of the 3 disks.
![](<Images/28. lsblk.png>)

7. Install lvm2 package using `sudo yum install lvm2`. Run `sudo lvmdiskscan` command to check for available partitions.
![](<Images/29. install_lvm2_2.png>),![](<Images/29. install_lvm2.png>)

8. Use `sudo pvcreate` utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM.
```
    sudo pvcreate /dev/xvdf1
    sudo pvcreate /dev/xvdg1
    sudo pvcreate /dev/xvdh1
```
![](<Images/30. sudo_pvcreate_2.png>)
 
Verify that the physical volume has been created using `sudo pvs` - ![](<Images/31. sudo_pvs.png>)

9. Use `sudo vgcreate` utility to add all 3 PVs to a volume group (VG). Let the Volume Group be named `webdata-vg`
`sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1` 

10. Verify that the volume group has been created using `sudo vgs`

![](<Images/32. vg create.png>)

11. Use `sudo lvcreate` utility to create 2 logical volumes. 
    `db-lv` (Use half of the PV size), and `logs-lv` Use the remaining space of the PV size. 
    **NOTE: db-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.**
```
    sudo lvcreate -n db-lv -L 14G webdata-vg
    sudo lvcreate -n logs-lv -L 14G webdata-vg
```
![](<Images/33. sudo_lvc.png>)

12. Verify that your Logical Volume has been created successfully by running `sudo lvs` - ![](<Images/34. sudo_lvs.png>)

13. Verify the entire setup by running `sudo vgdisplay -v`
![](<Images/35. full_display_1.png>),![](<Images/35. full_display_2.png>)

14. Use `mkfs.ext4` to format the logical volumes with `ext4` filesystem
```
    sudo mkfs -t ext4 /dev/webdata-vg/db-lv
    sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```
![](<Images/36. mk_format.png>)

15. Create `/var/www/html` directory to store website files - `sudo mkdir -p /db`
16. Create `/home/recovery/logs` to store backup of log data -`sudo mkdir -p /home/recovery/logs`
17. Mount `/var/www/html` on apps-lv logical volume - `sudo mount /dev/webdata-vg/db-lv /db/`
18. Use `rsync` utility to backup all the files in the log directory `/var/log into /home/recovery/logs` (This is required before mounting the file system) - 
`sudo rsync -av /var/log/. /home/recovery/logs/`
19. Mount `/var/log` on logs-lv logical volume. 
(**Note that all the existing data on /var/log will be deleted. That is why step 14 above is very important**) - `sudo mount /dev/webdata-vg/logs-lv /var/log`
20. Restore log files back into /var/log directory -`sudo rsync -av /home/recovery/logs/. /var/log`
![](<Images/37. adjust_2.png>)

21. Update `/etc/fstab` file so that the mount configuration will persist after restart of the server
    The UUID of the device will be used to update the /etc/fstab file;
    `sudo blkid`
    `sudo vi /etc/fstab`

    e468af0a-2e6e-4910-841c-6de178bae519 - apps
    1bba0cfd-9c19-4428-8756-97612838e37a - logs
![](<Images/38. bld_id.png>)
![](<Images/39. vi_changes.png>)

Test the configuration and reload the daemon
```
    sudo mount -a
    sudo systemctl daemon-reload
```
![](<Images/40. reload.png>)

Verify your setup by running `df -h`, output should look like this - ![](<Images/41. df_h_2.png>)

## Install Wordpress on the Webserver EC2 Instance
1. Update the repository - `sudo yum -y update` - ![](<Images/42. update_repo.png>)
2. Install wget, Apache and it's dependencies - `sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json` - ![](<Images/43. apache.png>)
3. Start Apache
```
    sudo systemctl enable httpd
    sudo systemctl start httpd
```
![](<Images/44. start_apache.png>)

4. Install PHP and it's dependencies
```
    sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
    sudo yum module list php
    sudo yum module reset php
    sudo yum module enable php:remi-7.4
    sudo yum install php php-opcache php-gd php-curl php-mysqlnd
    sudo systemctl start php-fpm
    sudo systemctl enable php-fpm
    sudo setsebool -P httpd_execmem 1
```
![](<Images/45. install_php_1.png>)
![](<Images/45. install_php_1.png>)
![](<Images/45. install_php_3.png>)

5. Restart Apache `sudo systemctl restart httpd`

### Download wordpress and copy wordpress to /var/www/html
```
    mkdir wordpress
    cd   wordpress
    sudo wget http://wordpress.org/latest.tar.gz
    sudo tar xzvf latest.tar.gz
    sudo rm -rf latest.tar.gz
    sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
    sudo cp -R wordpress /var/www/html/
```
![](<Images/46. wordpress.png>)

### Configure SELinux Policies
```
    sudo chown -R apache:apache /var/www/html/wordpress
    sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
    sudo setsebool -P httpd_can_network_connect=1
    sudo setsebool -P httpd_can_network_connect_db 1
```
![](<Images/47. selinux.png>)

### Install MySQL On EC2 DB Server
```
    sudo yum update
    sudo yum install mysql-server
```
![](<Images/48. yum_db.png>)
![](<Images/49. mysql_server.png>)

Verify that the service is up and running by using `sudo systemctl status mysqld`, if it is not running, restart the service and enable it so it will be running even after reboot:
![](<Images/50. systemctl.png>)
```
    sudo systemctl restart mysqld
    sudo systemctl enable mysqld
```
![](<Images/51. updated_status.png>)

### Configure DB to Work With WordPress
```
    sudo mysql

    CREATE DATABASE wordpress;

    CREATE USER 'ec2-user'@'172.31.29.242' IDENTIFIED BY 'password';

    GRANT ALL ON wordpress.* TO 'ec2-user'@'172.31.29.242';

    FLUSH PRIVILEGES;

    SHOW DATABASES;

    exit
```    
![](<Images/52. sql_db.png>)

### Configure WordPress to connect to remote database
**We will open MySQL port 3306 on DB Server EC2. For extra security, we will allow access to the DB server ONLY from our Webserver's IP address, so in the inbound rule configuration specify source as /32**
![](<Images/53. enable_3306.png>)

1. Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client - 
```
    sudo yum install mysql
    sudo mysql -u ec2-user -p -h 172.31.35.132
```
![](<Images/54. mysql_wbs.png>)
Verify if you can successfully execute SHOW DATABASES; command and see a list of existing databases.
![](<Images/55. connect_wbs.png>)

2. Change permissions and configuration so Apache could use WordPress
3. Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2 (enable from everywhere 0.0.0.0/0 or from your workstation’s IP)
![](<Images/56. http_allow.png>)
4. On the web server, edit wordpress configuration file - `sudo vi /var/www/html/wordpress/wp-config.php`
![](<Images/57. edit_conf.png>)

Try to access from your browser the link to your WordPress - `http://18.207.168.37/wordpress`
![](<Images/58. wordpress_1.png>)
![](<Images/59. wordpress_2.png>)
![](<Images/60. wordpress_3.png>)
![](<Images/61. wordpress_4.png>)

## Conclusion: Anchors Away, Your WordPress Voyage Begins Now!
As we wrap up this project, remember: the journey doesn't end here. Armed with newfound LVM mastery and WordPress prowess, you're equipped to conquer new horizons. Keep exploring, keep innovating, and keep pushing the boundaries of what's possible. Your WordPress adventure has only just begun. Bon voyage!