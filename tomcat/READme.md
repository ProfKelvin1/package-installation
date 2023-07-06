#  **<span style="color:green">Sysmsify Technologies.</span>**
### **<span style="color:green">Contacts: +1647-864-0494<br> WebSite : <https://sysmsify.com/></span>**
### **Email: hr@sysmsify.com**

## Apache Tomcat Installation And Setup In AWS EC2 Redhat Instance.
##### Prerequisite
+ AWS Acccount.
+ Create Redhat EC2 T2.micro Instance.
+ Create Security Group and open Tomcat ports or Required ports.
   + 8080 ..etc
+ Attach Security Group to EC2 Instance.
+ Install java openJDK 1.8+

### Install Java JDK 1.8+ 

``` sh
# change hostname to tomcat
sudo hostnamectl set-hostname tomcat
sudo su - ec2-user
sudo yum update
cd /opt 
sudo yum update -y

# install wget unzip git packages.
sudo yum install git wget unzip -y

# install Java  as a pre-requisite for tomcat to run.
#list possible versions in yum package repo
sudo yum list java-*-openjdk-devel

#choose your version, we are choosing version 11
sudo yum install java-11-openjdk-devel -y

```
## Install Tomcat version 9.0.75
### Download and extract the tomcat server
``` sh
sudo wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.10/bin/apache-tomcat-10.1.10.zip
sudo unzip apache-tomcat-10.1.10.zip
sudo rm -rf apache-tomcat-10.1.10.zip

### rename tomcat for good naming convention
sudo mv apache-tomcat-10.1.10 tomcat10

### assign executable permissions to the tomcat home directory
sudo chmod 777 -R /opt/tomcat10
sudo chown ec2-user -R /opt/tomcat10

### start tomcat
sh /opt/tomcat10/bin/startup.sh

# create a soft link to start and stop tomcat
# This will enable us to manage tomcat as a service
sudo ln -s /opt/tomcat10/bin/startup.sh /usr/bin/starttomcat
sudo ln -s /opt/tomcat10/bin/shutdown.sh /usr/bin/stoptomcat

starttomcat
sudo su - ec2-user
```


