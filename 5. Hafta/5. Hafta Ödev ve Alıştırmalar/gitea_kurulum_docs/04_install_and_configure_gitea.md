# Gitea installation 
https://www.youtube.com/watch?v=y9zDbMkuXdE


## Database preperation
https://docs.gitea.io/en-us/database-prep/

[train@localhost play]$ sudo -u postgres psql
CREATE ROLE gitea WITH LOGIN PASSWORD 'gitea';
CREATE DATABASE giteadb WITH OWNER gitea TEMPLATE template0 ENCODING UTF8 LC_COLLATE 'en_US.UTF-8' LC_CTYPE  'en_US.UTF-8';

Followings should work  

[train@localhost play]$ psql "postgres://gitea@localhost/giteadb"

## From binary 
[train@localhost ~]$ mkdir -p /opt/manual/gitea_home 
cd /opt/manual/gitea_home
wget -O gitea https://dl.gitea.io/gitea/1.12.5/gitea-1.12.5-linux-amd64
chmod +x gitea

Add envronment file 
export GITEA_WORK_DIR=/var/lib/gitea/

Add this variable into /etc/environment file with sudo nano  
source /etc/environment

[train@localhost manual]$ sudo mkdir -p /var/lib/gitea/{custom,data,log}
[train@localhost manual]$ sudo useradd git
[train@localhost manual]$ sudo chown -R git:git /var/lib/gitea/
[train@localhost manual]$ sudo chmod -R 750 /var/lib/gitea/
[train@localhost manual]$ sudo mkdir /etc/gitea
[train@localhost manual]$ sudo chown root:git /etc/gitea
[train@localhost manual]$ sudo chmod 770 /etc/gitea


[train@localhost manual]$ sudo cp gitea_home/gitea /usr/local/bin/gitea

[train@localhost manual]$ sudo GITEA_WORK_DIR=/var/lib/gitea/ /usr/local/bin/gitea web -c /etc/gitea/app.ini


- It will open gitea go to browser localhost:3000 

- On browser click register  
Use the two screen shot and provide info in there.


- Register for new user 
New user jenkins password Ankara_06  


## Install jenkins if not already installed.

- Install jenkins with yum -y install jenkins  

- Install gitea plugin on jenkins plugin  

jenkins user must be in docker group 
[train@localhost ~]$ sudo usermod -aG docker jenkins
[train@localhost flask_ci_cd]$ id jenkins
uid=986(jenkins) gid=979(jenkins) groups=979(jenkins),980(docker)

Gitea Jenkins integration  

https://stackoverflow.com/questions/48316346/gitea-and-jenkins-webhook

