Environment:
Cloudera Manager 5.8.3
Cloudera Distributed Hadoop 5.8.3
Install jdk8u131 (recommened at the time of this writing) on all the hosts involved in the cluster 

On the Cloudera Manager Server:
Add the cloudera-manager.repo file to /etc/yum.repos.d with contents below
	[cloudera-manager]
	# Packages for Cloudera Manager, Version 5, on RedHat or CentOS 6 x86_64           	  
	name=Cloudera Manager
	baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5.8.3/
	gpgkey =https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/RPM-GPG-KEY-cloudera    
	gpgcheck = 1

yum install "cloudera-*"

Setup the MySQL:
	CREATE DATABASE scm_lab;
	CREATE USER 'scm_lab'@'%' IDENTIFIED BY 'scm_lab';
	GRANT ALL ON scm_lab.* TO 'scm_lab'@'%';
	FLUSH PRIVILEGES;
	
	CREATE USER lab_hive@'%' IDENTIFIED BY 'lab_hive';
	GRANT ALL ON lab_metastore.* TO 'lab_hive'@'%';
	FLUSH PRIVILEGES;


	CREATE DATABASE lab_amon DEFAULT CHARACTER SET utf8;
	CREATE USER lab_amon@'%' IDENTIFIED BY 'lab_amon';
	GRANT ALL ON lab_amon.* TO 'lab_amon'@'%';
	FLUSH PRIVILEGES;


	CREATE DATABASE lab_rman DEFAULT CHARACTER SET utf8;
	CREATE USER lab_rman@'%' IDENTIFIED BY 'lab_rman';
	GRANT ALL ON lab_rman.* TO 'lab_rman'@'%';
	FLUSH PRIVILEGES;


On Cloudera Manager server update /etc/cloudera-scm-server/db.properties with the MySQL details (User/Pass/Host etc)

Create /usr/share/java directory on all nodes where you need to have connectivity to MySQL (scm,amon,rman,hivemetastore,oozie etc)

Download MySQL JDBC jar from oracle website and rename the file as mysql-connector-java.jar


Install Kerberos:
On the KDC Host run "yum install krb5-server"
Edit /etc/krb5.conf file to update the REALM, kdc host and DOMAIN_REALM
Update encryption types in the krb5.conf under libdefaults section
	 default_tgs_enctypes = aes256-cts-hmac-sha1-96 des-cbc-md5
	 default_tkt_enctypes = aes256-cts-hmac-sha1-96 des-cbc-md5
Update /var/kerberos/krb5kdc/kadm5.acl [Include principal that has admin privileges]
Update /var/kerberos/krb5kdc/kdc.conf [REALM, ticket renewal life]
Create krb5 database: "kdb5_util create -s" [Remember the password]
Start the krb5kdc: service krb5kdc start [This starts the key distribution center (kdc)]
Start the Admin Server: service kadmin start [This starts the kerberos admin server]

Notes:
One the KDC host you should see the below scripts under /etc/init.d 
[root@localhost init.d]# pwd
/etc/init.d
[root@localhost init.d]# ls -lrt k*
-rwxr-xr-x 1 root root  2815 Nov 22  2016 krb5kdc
-rwxr-xr-x 1 root root  2055 Nov 22  2016 kprop
-rwxr-xr-x 1 root root  2488 Nov 22  2016 kadmin


On every node in the Hadoop Cluster install the krb5 workstation
"yum install krb5-workstation"
Copy the /etc/krb5.conf from the KDC server and put it in the same location (same file name) on all the Hadoop Nodes.
Copy OracleJDK unlimited JCE policy files
Create a principal for ClouderaManager:
On the kdc server;sudo kadmin.local
kadmin.local: addprinc scm/admin 
<enter a password and note it down>
Test the princpal created for SCM: On any other host run the command "kinit scm/admin" and enter the password. 
If everythig goes well upto this point then you have a working Kerberos installation to integrate it with Cloudera Manager

