# DevOpsToolingWebsiteSolution

1) Set up 5 instances
      4 instances - Redhat-webserver and nfs
      1 instance-Ubuntu-DB
2)Attach 3 ebs volumes to nfs
3)Set up nfs
      * login via ssh from the terminal
      * create partitions on the disks you've attached
      * install lvm2
      * create pv to be used by lvm
      * package all of pv to one volume group
      * create logical volumes lv-apps, lv-logs, lv- using the command "sudo lvcreate -n lv-apps -L 9G            webdata-vg"
      * format the disks as xfs instead of ext4 using the command "sudo mkfs -t xfs /dev/webdata-vg/lv-           opt"
      * create /mnt/apps, /mnt/logs, /mnt/opt directories and mount /dev/webdata-vg/lv-apps,           /dev/webdata-vg/lv-logs and /dev/webdata-vg/lv-opt on them respectively using the command "sudo mount /dev/webdata-vg/lv-apps /mnt/apps"
      * install nfs server
      * 
