# linux_hadoop_minimal_upgrade_centos_6_to_7

############################################################################################
Centos 6.10 to Centos 7.x Linux Hadoop Minimal, maybe work....
maybe...and just maybe by Rodrigo Vargas A. Santiago, Chile 
NOTE: Shannon C, wow wow wow, nice hair 1313 :) 
08/10/2019 

For the second class of Douglas Eadline of :
Hands-on Introduction to Apache Hadoop and Spark Programming
A quick-start introduction to the important facets of big data analytics
https://learning.oreilly.com/live-training/courses/hands-on-introduction-to-apache-hadoop-and-spark-programming/0636920326755/
 
for : Douglas endline (https://www.clustermonkey.net/scalable-analytics/doku.php?id=start)

####################################################
If you use KVM/QEMU then
Download : tar -xvf Linux-Hadoop-Minimal-0.42.ova
wget --no-check-certificate https://www.clustermonkey.net/download/Hands-on_Hadoop_Spark/Linux-Hadoop-Minimal-0.42.ova



then 
#tar -xvf Linux-Hadoop-Minimal-0.42.ova -> this create 3 files,
: .vmdk, .ovf(see it for conf of the virtual machine) and other .mf
Linux-Hadoop-Minimal-0.42.mf (not use)
Linux-Hadoop-Minimal-0.42.ovf (only for see the values for the virtual machine, if u want)
Linux-Hadoop-Minimal_1 1-disk001.vmdk  (this is important)
#qemu-img convert -f vmdk -O qcow2 Linux-Hadoop-Minimal_1\ 1-disk001.vmdk Linux-Hadoop-Minimal-0.42.qcow2
== If is neccesary resize then ===
-> qemu-img resize /var/lib/libvirt/images/Linux-Hadoop-Minimal-0.42.qcow2 +40G

then start the virt-manager and conf based in the value that appear in 
####################################################
Steps:1

1.- cat > /etc/yum.repos.d/centos-upgrade.repo <<EOF
[centos-upgrade]
name=centos-upgrade
baseurl=https://buildlogs.centos.org/centos/6/upg/x86_64/
enabled=1
gpgcheck=0
EOF

2.- yum -y install https://buildlogs.centos.org/centos/6/upg/x86_64/Packages/openscap-1.0.8-1.0.1.el6.centos.x86_64.rpm

3.- yum -y install preupgrade-assistant-contents redhat-upgrade-tool preupgrade-assistant

4.-  rpm --import http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-7

5.- mkdir -pv /var/tmp/system-upgrade/base/ /var/tmp/system-upgrade/extras/ /var/tmp/system-upgrade/updates/
6.- echo http://mirror.centos.org/centos/7/os/x86_64/ >> /var/tmp/system-upgrade/base/mirrorlist.txt
 7.-  echo http://mirror.centos.org/centos/7/extras/x86_64/ >> /var/tmp/system-upgrade/extras/mirrorlist.txt
 8.-   echo http://mirror.centos.org/centos/7/updates/x86_64/ >> /var/tmp/system-upgrade/updates/mirrorlist.txt
###Pre upgrade######
############
9.- yes | preupg -v
##############
#####Now upgrade #######
10.- centos-upgrade-tool-cli --network=7 --instrepo=http://vault.centos.org/7.0.1406/os/x86_64/
11.- reboot
12.- ip a
13.- dhclient
14.- Disabled Selinux -> setenforce 0
, but permanent -> vi /etc/selinux/config 
SELINUX=disabled
15.- MOST IMPORTANT STEP IS->
 link /usr/lib64/libpcre.so.1 /lib64/libpcre.so.0
16.- reboot
17.- dhclient ; systemctl  enable --now sshd
18.- ip a -> ssh root@IP -p 2222
19.-   systemctl enable blk-availability && systemctl start blk-availability.service
20.-    systemctl enable ip6tables && systemctl start ip6tables.service
21.-   systemctl enable iscsi && systemctl start iscsi.service
22.-  systemctl enable messagebus && systemctl start messagebus.service
23.- systemctl enable portreserve && systemctl start portreserve.service
24.-   systemctl enable postfix && systemctl start postfix.service
25.-   systemctl enable udev-post && systemctl start udev-post.service
26.- yum install NetworkManager
27.- systemctl enable --now NetworkManager
28.- yum install rpcbind nfs-utils -y
29.- wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
30.-    sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
31.-  yum update
32.-    sudo yum install mysql-server
33.-  systemctl enable --now mysqld
34.- cd   /usr/lib/jvm/
35.- mv java-1.8.0-openjdk-1.8.0.222.b10-1.el7_7.x86_64 java-1.8.0-openjdk.x86_64
36.- alternatives --install /usr/bin/java java /usr/lib/jvm/java-1.8.0-openjdk.x86_64/bin/java 1
37.- alternatives --install /usr/bin/javac javac /usr/lib/jvm/java-1.8.0-openjdk.x86_64/bin/javac 1
38.- alternatives --config java (Choose the java-1.8.0-openjdk.x86_64
39 .-    alternatives --config javac (Choose the same )

###################
Now start the services

/usr/sbin/start-hdfs.sh /usr/sbin/start-yarn.sh /usr/sbin/start-derby.sh /usr/sbin/start-hive-metastore.sh /usr/sbin/start-hiveserver2.sh /usr/sbin/start-zeppelin.sh

and so on……
and follow the page of Douglas Endline
https://www.clustermonkey.net/scalable-analytics/doku.php?id=start


###########################
[root@localhost ~]# cat /etc/*release*
CentOS Linux release 7.7.1908 (Core)
Derived from Red Hat Enterprise Linux 7.7 (Source)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.7.1908 (Core)
CentOS Linux release 7.7.1908 (Core)
cpe:/o:centos:centos:7

[root@localhost ~]# jps
2193 SecondaryNameNode
4242 SparkSubmit
4194 RemoteInterpreterServer
2405 ResourceManager
2470 NodeManager
2264 DataNode
2088 NameNode
4088 ZeppelinServer
6474 Jps
4154 RemoteInterpreterServer
2699 JobHistoryServer

And so on
######################

Cheers from Chile :)
