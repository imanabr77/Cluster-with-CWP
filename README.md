# Cluster-with-CWP
>Control Web Panel is a free modern and intuitive control panel for servers and VPS that enables the day to day management and their security easy. Considerations were taken into account through the development of the web panel in terms of security functionalities and the interface.

>The software in its entirety will remain free for use so as to be available to a wider audience as possible.

1. Modern Interface.
2. Security to the Core.
3. Premium Support Included.
4. Customizable Support.
5. Created by system admins for system admins.

![This is an image](https://github.com/imanabr77/Cluster-with-CWP/blob/main/Cluster.jpg)

# Config CWP FOR Cluster 

## Clustering
 > apache
 > DNS
 > My-SQL or Maria-DB

### I need 3 node for cluster :
- Node1_ip
- Node2_ip
- Node3_ip
 
### OS :  Cent os

and buy Domain.com .

## Update packages : 

- sudo dnf/yum install epel-release
- sudo yum/dnf update
- sudo yum/dnf install wget
- sudo reboot


# Set Hostname for Nodes  :

Node 1 :
- sudo hostnamectl set-hostname ns1.domain.com

Node 2 : 
- sudo hostnamectl set-hostname ns2.domain.com

Node 3 : 
- sudo hostnamectl set-hostname ns3.domain.com

and [ sudo reboot ]

# CWP script execution

- cd /usr/local/src


For using Centos7 : 
- sudo wget http://centos-webpanel.com/cwp-el7-latest
- sudo sh cwp-el-latest


Any Distro :
- sudo wget http://centos-webpanel.com/cwp-el8-latest
- sudo sh cwp-el8-latest



After installing CWP, we enter CWP through server IP and port 2030
http://server-ip:2030 and use the username and password of the server to enter CWP
we do.



After logging in, go to IP Nameservers Edit>DNS Functions from the left menu
And like the picture below
Place the server name and IP of node 1 and node 2 and click the save button
we are pressing

From the account section, you can add up to ten accounts in the free version



#Communication between servers


Create an SSH key on each server and upload it to other servers
Then the following file with
Run the following command on all three servers

- sudo vim /etc/resolv.conf
- node1_ip ns1.domain.com
- node2_ip ns2.domain.com
- node3_ip ns3.domain.com



# Database clustering

For database clustering, we use a service called cluster galera, which with installation
 cwp, this package is installed and only needs to be launched.
Since galera cluster only works with InnoDB type, it is necessary that all table
Change CWPs to InnoDB type. To work through the CWP of any server
Go to phpmyadmin and click on the CWP_root database in the root branch


And change all the tables except settings to InnoDB for this first
 Click on the desired table, then go to operations
Go and then go to the engine storage section and change its type to InnoDB and the button
Press Go.


## Changing the password of the root user in nodes 1 and 2:


To do this, we enter the database environment with the **My-sql -P**  command and enter the root password with this command
we change


- ALTER USER 'root'@'localhost' IDENTIFIED BY 'node1_mysql_password';


Then we change the old mysql root passwords in the following files in node 2 and node 3.

- /root/.my.cnf >>> change password=
- /usr/local/cwpsrv/htdocs/resources/admin/include/db_conn.php >> change $db_pass=


Then, with the following command, we first stop the database in all three nodes

- sudo systemctl stop mariadb


and open the database settings file in all three nodes:

- sudo vim /etc/my.cnf.d/server.cnf


and in **[galera]** section
Add the following settings:

- binlog_format=ROW
- default_storage_engine=InnoDB
- innodb_autoinc_lock_mode=2
- bind-address=0.0.0.0
- wsrep_on=ON
- wsrep_provider=/usr/lib64/galera-4/libgalera_smm.so
- wsrep_cluster_name="mycluster"
- wsrep_cluster_address="gcomm://node1_ip,node2_ip.node3_ip"
- wsrep_sst_method=rsync
- wsrep_node_address="node1_ip"
- wsrep_node_name="node_name"

### Description:
