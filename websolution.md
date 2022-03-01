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
 
10.Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg
 
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
 
Verify that your VG has been created successfully by running sudo vgs
 
 <img width="437" alt="vgs" src="https://user-images.githubusercontent.com/82297594/156093721-7becbd64-0977-444e-9c34-3f3b2483f8d7.png">

 
