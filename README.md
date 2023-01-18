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
 
 
 5) Now ssh to webserver1 via the terminal
