# Steps 

# Step 1: 
Launch 2 instances for Devloper Dev1 and Dev2 and install Git in both pcs

sudo hostnamectl set-hostname dev1
sudo hostnamectl set-hostname dev1
sudo su
#yum install git -y
#mkdir /punegit
#cd /punegit
#git init
Create user and email for git operation
#git config --global user.name sara
#git config --global user.email sara@gmail.com

For Verify user details
#git config --list
#cd ..
#git clone https://github.com/hackwithabhi1/hello-world.git
#cp -rvf hello-world/* /punegit/
#cd  /punegit/
#git  add .
#git commit -m 'myawspro' .
#git status

# Step2: 
create repository in github.com

#git branch -M main
#git remote add origin https://github.com/Abhishekphapale/AWSProject.git
#git push https://<token>@github.com/hackwithabhi/awspromarch2024.git

# Step3: 
setup jenkins server

Launch server and give it name 'Jenkins'

#hostnamectl  set-hostname jenkins
#hostname
#bash

Install java

#yum update -y

#yum install java-11-amazon-corretto-headless -y
#yum install java-11-amazon-corretto -y
#yum install java-11-amazon-corretto-devel -y
#yum install java-11-amazon-corretto-jmods -y
#java -version

Create tomcat user and group

#groupadd --system tomcat
#useradd -d /usr/share/tomcat -r -s /bin/false -g tomcat tomcat

Install Tomcat 9
#wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.63/bin/apache-tomcat-9.0.63.tar.gz
#tar -xvzf apache-tomcat-9.0.63.tar.gz -C /usr/share/
Create Symlink to the folder /usr/share/tomcat. This is for easy updates.
#ln -s /usr/share/apache-tomcat-9.0.63/ /usr/share/tomcat
#chown -R tomcat:tomcat /usr/share/tomcat
#chown -R tomcat:tomcat /usr/share/apache-tomcat-9.0.63/

#tee /etc/systemd/system/tomcat.service<<EOF
[Unit]
Description=Tomcat Server
After=syslog.targetnetwork.target

[Service]
Type=forking
User=tomcat
Group=tomcat

Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment='JAVA_OPTS=-Djava.awt.headless=true'
Environment=CATALINA_HOME=/usr/share/tomcat
Environment=CATALINA_BASE=/usr/share/tomcat
Environment=CATALINA_PID=/usr/share/tomcat/temp/tomcat.pid
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M'
ExecStart=/usr/share/tomcat/bin/catalina.sh start
ExecStop=/usr/share/tomcat/bin/catalina.sh stop


[Install]
WantedBy=multi-user.target
EOF

Enable and start tomcat service:

#systemctl daemon-reload
#systemctl start tomcat
#systemctl enable tomcat
#systemctl status tomcat

#vim /usr/share/tomcat/conf/tomcat-users.xml

<role rolename="admin-gui"/>
<role rolename="manager-gui"/>
<user username="admin" password="111" fullName="Administrator" roles="admin-gui,manager-gui"/>
:wq

#yum install httpd  -y

#vim /etc/httpd/conf.d/tomcat_manager.conf
<VirtualHost *:80>
ServerAdmin root@localhost
ServerName tomcat.example.com
DefaultType text/html
ProxyRequests off
ProxyPreserveHost On
ProxyPass / http://localhost:8080/
ProxyPassReverse / http://localhost:8080/
</VirtualHost>

<VirtualHost *:80>
ServerName ajp.example.com
ProxyRequests Off
ProxyPass / ajp://localhost:8009/
ProxyPassReverse / ajp://localhost:8009/
</VirtualHost>

:wq


#systemctl restart httpd
#systemctl enable httpd
#systemctl status httpd

http://192.168.1.111

username: admin	
password: 111

#cd /usr/share/tomcat/
#ls
#cd webapps

#wget https://get.jenkins.io/war-stable/2.414.3/jenkins.war
#systemctl restart tomcat

Get the login password of Jenkins from below address:
#cat /usr/share/tomcat/.jenkins/secrets/initialAdminPassword
Copy paste
username: admin
password: 111


# steup for java and maven
Verify Version
#java -version

#cd
#vim .bash_profile
JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
 PATH=$PATH:$JAVA_HOME:$HOME/bin
:wq

#echo $PATH
#source .bash_profile
#echo $PATH

#cd /opt/
#wget  https://dlcdn.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.tar.gz
 #tar -xvzf  apache-maven-3.8.8-bin.tar.gz
#rm -rvf  *.gz
#mvn –version

#cd
#vim .bash_profile
MAVEN_HOME=/opt/apache-maven-3.8.8
M2=/opt/apache-maven-3.8.8 /bin/
PATH=$PATH:$JAVA_HOME:$MAVEN_HOME:$M2:$HOME/bin
:wq

#source .bash_profile
#echo $M2
#echo $MAVEN_HOME
#mvn  --version


#yum update -y
#yum install git -y
#git  --version
#which git

# Step 4

Open Jenkins
http://localhost/jenkins

Dashboard – Manage Jenkins – Tools – git – Name (default) – path to git execute (/bin/git)
Maven – add maven – Name (mvn) – MAVEN_HOME(/opt/apache-maven-3.8.8/)– Apply – Save.

Dashboard – New Item – Enter new item name (aws_pro) – freestyle project – ok – source code management – git – repository url ( https://github.com/Abhishekphapale/AWSProject.git ) - Build Steps (invoke top-level maven targets ) – Maven version (mvn) – Goals ( clean install package ) – Apply – Save.

Dashboard – dev_pro – Build Now – console output.

# Step 5
Create Ansible Server: 

#hostnamectl set-hostname ansible
install ansible
#yum install ansible -y
#ansible --version

#useradd   auser
#passwd   auser
111
111
#vim /etc/sudoers
auser  ALL=(ALL)   NOPASSWD: ALL   # ( on line 101)
:wq

update ssh config file on ansible and all docker server
#vim  /etc/ssh/sshd_config
PermitRootLogin yes                                ( line 40 )
PasswordAuthentication yes                ( line 65 )
:wq
#systemctl  restart  sshd

:wq!

#su auser
$ssh-keygen

$ssh-copy-id  auser@192.168.1.113 

#sudo mkdir /opt/playbook
$sudo  vim  /opt/playbook/inventory

[docker]
192.168.1.113
:wq

#sudo vim /opt/playbook/ansible.cfg

[defaults]
inventory=/opt/playbook/inventory
remote_user=auser
host_key_checking=false
[privilege_escalation]
become=true
become_user=root
become_method=sudo
become_ask_pass=false
:wq

cd   /opt/playbook/
$ansible  all  -m  ping
$su root

Install docker on ansible server

#yum install docker -y
#yum info docker
#systemctl start docker
#systemctl enable docker
#systemctl status docker
#usermod -aG docker auser
#mkdir /opt/docker
#chown -R auser:auser /opt/Docker
su auser
$sudo vim /opt/playbook/create-docker-container.yml

 - name: create docker container
   hosts: all
   ignore_errors: yes
   tasks:
     - name: install docker
       yum:
         name: docker
         state: present
     - name: start and enable docker
       service:
         name: docker
         state: started
         enabled: yes
     - name: add user into group
       command: usermod -aG docker auser
     - name: change ownership
       command: chown -R auser:auser /opt/docker
     - name: stop the running container
       shell: docker stop webapp
     - name: remove container
       shell: docker rm -f webapp
     - name: remove container image
       shell: docker rmi -f sevenmentor:latest
     - name: transfer docker file
       copy:
         src: /opt/docker/Dockerfile
         dest: /opt/docker/
     - name: trasfer web appplication
       copy:
         src: /opt/docker/webapp.war
         dest: /opt/docker/
     - name: build docker image
       shell: cd /opt/docker; docker build -t NewContainer .
     - name: create docker tomcat container
       shell: docker run -dt --name webapp -p 8090:8080 NewContainer:latest

:wq	

In Jenkins:

Dashboard- manage Jenkins – manage plugins – available – install “publish over ssh” plugins – restart Jenkins.

Dashboard – manage Jenkins – configure system – system –SSH Servers (add ) – Name (Ansible_Server) Hostname (192.168.1.112) – username (auser) – Advanced - use password authentication or use a different key (111) – Test Configuration – Apply - Save.
Open Dashboad – DevOps_Project – Configure-
Now click on “Post Build Action” – send build artifacts over SSH – Name (Ansible_Server) – Source files (webapp/target/*.war) – Remove prefix (webapp/target/) – Remote directory (//opt/docker)
Add Server
Name (Ansible_Server) – Source files (Dockerfile) – Remote directory (//opt/docker)
Add Server
Exec command
cd /opt/playbook; ansible-playbook create-docker-container.yml

Save - Apply

Goto docker server
#docker images
#dockerps
#dockerps -a
#ifconfig
Run Project
Go to Jenkins dashboard – run created project (right click and build)

Open web browser –
http://dockerip:8090/webapp

# Done !!!
