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
sudo yum update -y
cd /opt 

# install wget unzip git packages.
sudo yum install git wget vim unzip -y

# install Java  as a pre-requisite for tomcat to run.
#list possible versions in yum package repo
sudo yum list java-*-openjdk-devel

#choose your version, we are choosing version 11
sudo yum install java-11-openjdk-devel -y

```
## Install Tomcat version 9.0.75
### Download and extract the tomcat server
``` sh
sudo wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.15/bin/apache-tomcat-10.1.15.zip
sudo unzip apache-tomcat-10.1.15.zip
sudo rm -rf apache-tomcat-10.1.15.zip

### rename tomcat for good naming convention
sudo mv apache-tomcat-10.1.15 tomcat10

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

## Enable Manager app and create users & roles
``` sh
Go to this dir /opt/tomcat10/webapps/manager/META-INF
vi to the context.xml and comment the line betwwen the <context> tag

restart the tomcat server and check the web browser to see if the manager app is enabled.

To create users and password
vi to /opt/tomcat10/conf/tomcat-users.xml

Paste this on a new line (outside of multi-line comment
<user username="admin" password="admin123" roles="manager-gui,admin-gui,manager-script"/>

Restart your tomcat server again

```

## To Deploy your application  / artifact
### Use need to paste your artifact in the webapp dir/folder
``` sh
Using SSH Key for Authentication
scp -i keyname.pem filename user@destination-IPaddress:/absolute-path
scp -i key.pem maven-web-app.war ec2-user@54.147.134.171:/opt/tomcat10/webapps

Using Password Authentication
scp filename username@destination-ipaddress:/absolute-path
scp -r directoryname username@ipaddress:/home/ubuntu

To configure Password Authentication
sudo vi /etc/ssh/sshd_config    and search for Password Authentication and change to `Yes`
sudo systemctl restart sshd    #reestart the Service so that your changes will be active
sudo passwd ec2-user    #to set new password for ec2-user

```
## NGINX Installation
```sh
T2.medium Redhat Server

Open port 80 and 443 (HTTP & HTTPS)

sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx

cd /etc
cd /etc | grep nginx

cd nginx

sudo vi nginx.conf
```
## NGINX webserver acts as a proxy server a.k.a load balancer
```sh
events{
worker_connections 1024;
}
http { keepalive_timeout 5;
upstream tomcatServers  {
        keepalive 50;
        server 172.31.19.111:8080;
        server 172.31.21.48:8080;
        server 172.31.21.48:8080;
}
server  {
        listen 80;
location / {
        proxy_set_header X-Real-lP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header        Host $host;
        proxy_pass http://tomcatServers;
}       
}
}
```


```sh
sudo nginx -t

#and reload nginx will this command
sudo nginx -s reload

sudo setsebool -P httpd_can_network_connect true

sudo systemctl restart nginx

```
