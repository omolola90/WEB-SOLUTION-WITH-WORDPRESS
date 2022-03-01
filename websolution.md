#  WEB SOLUTION WITH WORDPRESS

In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

This Project  consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.

As a DevOps engineer, your deep understanding of core components of web solutions and ability to troubleshoot them will play essential role in your further progress and development.

Three-tier Architecture
Generally, web, or mobile solutions are implemented based on what is called the Three-tier Architecture.

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

<img width="488" alt="3tier" src="https://user-images.githubusercontent.com/82297594/156088062-7cf06ec1-b09d-4dd6-9327-d0840d9d8485.png">

1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
 
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
 
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. Database Server or File System Server such as FTP server, or NFS Server
In this project, you will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

You will be working working with several storage and disk management concepts to have a better understanding of logical volume management

Note: We are gradually introducing new AWS elements into our solutions, but do not be worried if you do not fully understand AWS Cloud Services yet, there are Cloud focused projects ahead where we will get into deep details of various Cloud concepts and technologies – not only AWS, but other Cloud Service Providers as well.

Your 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server
Use RedHat OS for this project

In previous projects we used ‘Ubuntu’, but it is better to be well-versed with various Linux distributions, thus, for this projects we will use very popular distribution called ‘RedHat’ (it also has a fully compatible derivative – CentOS)

Note: for Ubuntu server, when connecting to it via SSH/Putty or any other tool, we used ubuntu user, but for RedHat you will need to use ec2-user user. Connection string will look like ec2-user@<Public-IP>

Let us get started!
 
  # LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER”.
Step 1 — Prepare a Web Server
Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
  
  <img width="960" alt="WEB VOLUME" src="https://user-images.githubusercontent.com/82297594/156089821-eb18c1fd-85d8-4f65-ad54-563eb953e184.png">
 
2. Attach all three volumes one by one to your Web Server EC2 instance
 
  <img width="559" alt="at" src="https://user-images.githubusercontent.com/82297594/156090606-4a4a9efc-7bda-4e93-ad60-49f1c04490dc.png">
 
 3.Open up the Linux terminal to begin configuration

Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.
 
 <img width="309" alt="ATT VOL" src="https://user-images.githubusercontent.com/82297594/156091105-97a97394-72f7-4789-8201-8da7b2eeb1fc.png">
 
4. Use df -h command to see all mounts and free space on your server

5. Use gdisk utility to create a single partition on each of the 3 disks

sudo gdisk /dev/xvdf
 
<img width="605" alt="lv" src="https://user-images.githubusercontent.com/82297594/156091429-9821d413-eb89-49c1-b58d-cee101c2905e.png">
 
<img width="494" alt="lv2" src="https://user-images.githubusercontent.com/82297594/156091458-b1e9bae5-f8b8-4a9f-8d38-d003a7e2a8f0.png">
 
6. Use lsblk utility to view the newly configured partition on each of the 3 disks.
 
 <img width="365" alt="lsblk" src="https://user-images.githubusercontent.com/82297594/156091585-a6672c03-ce50-4dd7-ab41-2c23e61b4f4f.png">
 
7. Install lvm2 package using sudo yum install lvm2. Run sudo lvmdiskscan command to check for available partitions.
Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

8.Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1

9. Verify that your Physical volume has been created successfully by running sudo pvs
 <img width="517" alt="pvs" src="https://user-images.githubusercontent.com/82297594/156093468-cfb2d290-0f12-4954-9886-6cae623ea6ef.png">
 
10. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
 
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
 
11. Verify that your VG has been created successfully by running sudo vgs
 
 <img width="437" alt="vgs" src="https://user-images.githubusercontent.com/82297594/156093721-7becbd64-0977-444e-9c34-3f3b2483f8d7.png">
 
12. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
 
sudo lvcreate -n apps-lv -L 14G webdata-vg
 
sudo lvcreate -n logs-lv -L 14G webdata-vg
 
13.Verify that your Logical Volume has been created successfully by running sudo lvs
 
 <img width="576" alt="applv" src="https://user-images.githubusercontent.com/82297594/156094329-64cc389d-34b9-4cf6-a8c2-32557c5380bd.png">
 
14.  Verify the entire setup

sudo vgdisplay -v #view complete setup - VG, PV, and LV
 
sudo lsblk 
 
 <img width="307" alt="no" src="https://user-images.githubusercontent.com/82297594/156095947-6d529294-45d3-459f-b93c-36158887f37a.png">
 
15. Use mkfs.ext4 to format the logical volumes with ext4 filesystem

sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
 
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
16. Create /var/www/html directory to store website files

sudo mkdir -p /var/www/html

17. Create /home/recovery/logs to store backup of log data

sudo mkdir -p /home/recovery/logs

18. Mount /var/www/html on apps-lv logical volume

sudo mount /dev/webdata-vg/apps-lv /var/www/html/
 
19. Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)
 
sudo rsync -av /var/log/. /home/recovery/logs/
 
20. Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 15 above is very
important)
sudo mount /dev/webdata-vg/logs-lv /var/log
21. Restore log files back into /var/log directory
sudo rsync -av /home/recovery/logs/. /var/log
22. Update /etc/fstab file so that the mount configuration will persist after restart of the server.

# UPDATE THE `/ETC/FSTAB` FILE
The UUID of the device will be used to update the /etc/fstab file;

sudo blkid
 
  <img width="875" alt="bld" src="https://user-images.githubusercontent.com/82297594/156096695-f5b49d8d-fe5f-44dc-b96e-6b6e34b8c31e.png">
 
 sudo vi /etc/fstab

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.
 
  <img width="596" alt="fstab" src="https://user-images.githubusercontent.com/82297594/156096848-d8a2f297-51dd-4bf4-87a9-aa7c62ba49fc.png">
 
1. Test the configuration and reload the daemon

 sudo mount -a
 sudo systemctl daemon-reload
2. Verify your setup by running df -h, output must look like this:
 
 <img width="608" alt="de" src="https://user-images.githubusercontent.com/82297594/156097112-87516abc-edf3-45c4-a808-e2497095fd22.png">
 
Step 2 — Prepare the Database Server
 
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

Step 3 — Install WordPress on your Web Server EC2
1. Update the repository

sudo yum -y update

2. Install wget, Apache and it’s dependencies

sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

3. Start Apache

sudo systemctl enable httpd
sudo systemctl start httpd 
 
 
4. To install PHP and it’s depemdencies

sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
 
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
 
sudo yum module list php
 
sudo yum module reset php
 
sudo yum module enable php:remi-7.4
 
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
 
sudo systemctl start php-fpm
 
sudo systemctl enable php-fpm
 
setsebool -P httpd_execmem 1
 
5. Restart Apache

sudo systemctl restart httpd

6. Download wordpress and copy wordpress to var/www/html

  mkdir wordpress
 
  cd   wordpress
 
  sudo wget http://wordpress.org/latest.tar.gz
 
  sudo tar xzvf latest.tar.gz
 
  sudo rm -rf latest.tar.gz
 
  cp wordpress/wp-config-sample.php wordpress/wp-config.php
 
  cp -R wordpress /var/www/html/
 
7. Configure SELinux Policies

 sudo chown -R apache:apache /var/www/html/wordpress
 
 sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
 
 sudo setsebool -P httpd_can_network_connect=1
 
 Step 4 — Install MySQL on your DB Server EC2
 
sudo yum update
 
sudo yum install mysql-server
 
Verify that the service is up and running by using sudo systemctl status mysqld, if it is not running, restart the service and enable it so it will be running even after reboot:

sudo systemctl restart mysqld
 
sudo systemctl enable mysqld
 
 <img width="505" alt="msql stat" src="https://user-images.githubusercontent.com/82297594/156103077-8621782b-bf54-42b0-a48a-97df3f89e181.png">

 Step 5 — Configure DB to work with WordPress
 
sudo mysql
 
CREATE DATABASE wordpress;
 
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
 
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
 
FLUSH PRIVILEGES;
 
SHOW DATABASES;
 
exit
 
 


 
