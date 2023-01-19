# DevOpsToolingWebsiteSolution

1) Set up 5 instances
      4 instances - Redhat-webserver and nfs
      1 instance-Ubuntu-DB
2) Attach 3 ebs volumes to nfs
3) Set up nfs
      * login via ssh from the terminal
      * create partitions on the disks you've attached
      * install lvm2
      * create pv to be used by lvm
      * package all of pv to one volume group
      * create logical volumes lv-apps, lv-logs, lv- using the command "sudo lvcreate -n lv-apps -L 9G            webdata-vg"
      * format the disks as xfs instead of ext4 using the command "sudo mkfs -t xfs /dev/webdata-vg/lv-           opt"
      * create /mnt/apps, /mnt/logs, /mnt/opt directories and mount /dev/webdata-vg/lv-apps,           /dev/webdata-vg/lv-logs and /dev/webdata-vg/lv-opt on them respectively using the command "sudo mount /dev/webdata-vg/lv-apps /mnt/apps"
      * install nfs server
      * Install, start and check the status of nfs server using the commands:
            sudo yum install nfs-utils -y
            sudo systemctl start nfs-server.service
            sudo systemctl enable nfs-server.service     
      * We next make sure to set permissions so that the webservers can read, write and execute files on nfs.
      * Create exports file under /etc
      * Paste the subnet CIDR of the web server. This is done to configure access to NFS for clients within the same subnet. Check this using the command : sudo exportfs -arv
      * Check which port is used by nfs and open it using security group.
            rpcinfo -p | grep nfs
      * Change inbound rules to the above shown port address by allowing access to subnet CIDR address.
      

4) On DB server:
      * SSH on DB server via the terminal
      * Install mysql-server
      * start mysql using the command "sudo mysql"
      * Create a database named 'tooling' using the command : create database tooling;
      * create user using the command: create user 'webaccess'@'172.31.16.0/20' identified by 'password';
      * grant privileges using the following commands:
            grant all privileges on tooling.* to 'webaccess'@'172.31.16.0/20';
            flush privileges;
      * change the bind-address to 0.0.0.0 (open this to change the bind-address: sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf)
      * Make changes in security group to mysql-aurora and enable connection with all network interfaces(0.0.0.0/0)

 
 
 5) Now ssh to webserver1 via the terminal
      * Install nfs client using the command "sudo yum install nfs-utils nfs4-acl-tools -y"
      * Mount /var/www and target nfs server's export for apps using the command "sudo mount -t nfs -o rw,nosuid 172.31.23.165:/mnt/apps /var/www"
      * Create touch.md file in /var/www path and then check on nfs server in the path /mnt/apps to see if touch.md file exists. An image of this verification is shown below(boxed in blue).
      * Open /etc/fstab and add the line "172.31.23.165:/mnt/apps /var/www nfs defaults 0 0". This is shown below(boxed in blue)
      * Install apache: "sudo yum install httpd -y"
      * Verify if apache is on /var/www in webserver. It should also be available on /mnt/apps on nfs server.
      * Locate the log files for apache(/var/log - you should find httpd file here) and mount it on nfs server's export for log using the command "sudo mount -t nfs -o rw,nosuid 172.31.23.165:/mnt/logs /var/log/httpd". Add another line in /etc/fstab.
      * Next fork the code from https://github.com/darey-io/tooling . (Install git using the command "sudo yum install git" if its not previously installed on webserver. )
      * Change to 'tooling' directory
      * Ensure that html folder from repository is deployed to /var/www/html . (sudo cp -R html/. /var/www/html)
      * Try to access webpage using public ip of webserver1. If it cannot be accesse then diable the selinux using the commands:sudo setenforce 0
 sudo vi /etc/sysconfig/selinux   (Here change to SELINUX=disabled)  
      * Now open functions.php file(sudo vi /var/www/html/functions.php) and make the following changes (as shown in the blue box).
      * Install mysql on webserver
      * Apply tooling.db.sql script. First connect to the db server via web server using the command: msql -h 172.31.27.131 -u webaccess -p tooling < tooling-db.sql
      * Move Redhat test page and reload the webpage. sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.backup
      * restart apache(sudo systemctl restart httpd). Then refresh the webpage. It should load the login page to your website as shown below.
