# nginx

## Install Nginx  :beginner:
 TODO: Before installing Nginx in your Linux You have to stop `iptables` and the `selinux`
  ```
   service iptables stop ; systemctl stop iptables
   setenforce 0

  ```
 In order to install nginx add EPEL repo to your /etc/yum.repos.d 

 1. Download using wget and install as below in RHEL7 Machine
 
   ` cd /tmp/; wget "https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm" ;yum install -y  epel-release-latest-7.noarch.rpm; yum insall -y nginx `

### Verify and start nginx
   
   ` systemctl status nginx ; systemctl start nginx `




