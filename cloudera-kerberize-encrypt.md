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

