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

  Check the process with `ps -eaf | grep -i nginx`

 If you are familiar with AWS Route53 Add a recordset and test with below...you can also test with IP as well.

![AWSROute53](screen2.png?raw=true)

Checking the Nginx INstallation in browser

![Browser](screen1.png?raw=true)


#### INTRO ABOUT NGINX 

  So People say it is  HTTPD Webserver some may say it also does proxy and reverse proxy and HTTP Caching someothers also says it beautifully does Loadbalancing and request handling and rolling updates in more efficient way... 
so here i am witnessing all those are absolutely true, and more importantly those are FREE of cost as well.

#### How NGINX more effective than apache

Eventhough the Apache is KING of HELL nginx keep gainig its traction in terms of WEB connectivity but why? Dosen't mater what is the webserver ultimately it should serve the web requests without any errors as far a i know the difference is APACHE and NGINX handling these type of requests very differently.

so as per wiki APACHE generates a thread like java for each request and does all its work in that thread itself so wiki says it occupies memory for each thread separately.....where as NGINX shares the memory across the threads asynchronously how means i don't know :heart_eyes: :heart_eyes: :heart_eyes::heart_eyes: they say like that so we follow :stuck_out_tongue_winking_eye: :stuck_out_tongue_winking_eye:

* so nginx maintains thousands and thousands of connections maintains keep alive of session states in a small memory  units

So the features ranging from  Bandwidth Throttling to URL REdirects/Rewriting to IPV6 Compatible to Geolocation of IP'S


### Directory Structure of nginx and its usage

```

[root@ansible3 etc]# pwd
/etc

The most important file which nginx uses is the nginx.conf is the main configuration file in which you can specify the worker connections log format include ssl virtual hosts ..etc

[root@ansible3 etc]# tree -A nginx
nginx
├── conf.d
├── default.d
├── fastcgi.conf
├── fastcgi.conf.default
├── fastcgi_params
├── fastcgi_params.default
├── koi-utf
├── koi-win
├── mime.types
├── mime.types.default
├── nginx.conf
├── nginx.conf.default
├── scgi_params
├── scgi_params.default
├── uwsgi_params
├── uwsgi_params.default
└── win-utf

2 directories, 15 files
[root@ansible3 etc]# tree -A nginx/conf.d/
nginx/conf.d/

0 directories, 0 files
[root@ansible3 etc]#

The default HTML directory after installation and it is just default we can change whenever we want literally :clap: :clap:

[root@ansible3 nginx]# cd /usr/share/nginx/html/
[root@ansible3 html]# pwd
/usr/share/nginx/html
[root@ansible3 html]# tree -A
.
├── 404.html
├── 50x.html
├── index.html
├── nginx-logo.png
└── poweredby.png

0 directories, 5 files

```

### Optimizing nginx.conf  /etc/nginx/nginx.conf

How to declare nginx global variables based on the system Resources available to us

1. First the backbone of nginx is workers that are worker_processes and  worker_conections

  ##### worker_processes
  This is used to tell how many workers to spawn after starting the nginx i mean once nginx bound to the proper IP Address and PORT. As per nginx wiki it is advised to use woker_processes based on the CPU cores(Processors) in your system check the cores with the command cat /proc/cpuinfo | grep -i Processor
 
We can set it as auto i.e.  worker_processes auto;


 * **worker_processes and  worker_conections** 

 ##### Worker_connections
 This tells to worker_proceses that  how many concurent connections NGINX could serve 
 soooo that means how many end users can be simultaneously served as a web connection on NGINX
 so this would be we can set as ulimit -n value because these many max files only our OS supports at a given point of time..if you know about little LINUX you can agree this  :clap:


### How to handle read write Effectively in NGINX 


* **BUFFERS** Buffers help nginx to make I/O so faster  and make your web requset/response in giffy :muscle: :muscle:  so buffers effectively optimize nginx connections...


