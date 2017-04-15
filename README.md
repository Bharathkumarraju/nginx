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

* **Sample NGINX Content**

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {

     worker_connections 1024;

}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```

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

* **CLIENT BODY BUFFER SIZE**
 This Handles POST actions that are sent to NGINX...typically Form Submissions

* **CLIENT HEADER BUFFER SIZE**
It is similar to above but it only handles HEADERS


* **CLIENT MAX BODY SIZE**
 this is the maximum allowed size for client request. you can declare it based on  how much memory  you have in your system, It can supports upto maximum memory available in your system

* **LARGE Client HEADER BUFFER**
this is for maximum size of large client headers

We have to include all above buffer sizes in the HTTP directive sample BUFFER size included in HTTP directive as below

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;
events {
     worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

   client_body_buffer_size 10k;
   client_header_buffer_size 1k;
   client_max_body_size 18m;
   large_client_header_buffers 2 1k;
    
    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}
```

## Timeouts in Nginx.conf

Timeouts drastically improves the performance of nginx 

Nginx that waits to send the body or header to be sent after the  client Request

* **Client Body TimeOut**
nginx wait time to send the body after the request made

* **Client Header TImeout**
nginx wait time to send the header after the request

* **KeepAlive Timeout**
 Nginx close the connections after this period of time

* **send timeout**

If After this time if client will take nothing means then nginx will shutdown the connection

We can even monitors these timeouts and alerts when website not loading :clap: Thanks to timeouts :clap: :clap:

` Below is the sample of nginx.conf with all config tweeks`

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
# Load dynamic modules. See /usr/share/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;
events {
     worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   15;
    types_hash_max_size 2048;

   client_body_buffer_size 10k;
   client_header_buffer_size 1k;
   client_max_body_size 18m;
   large_client_header_buffers 2 1k;
   client_body_timeout 12;
   client_header_timeout 12;
   send_timeout 10;

   include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;
        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;
        location / {
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}

```

## VHOST and some standard configurations

Let's comment server directive in the /etc/nginx/nginx.conf and add the server directive in the vhost.d as default.conf and add the root location as /var/www/html and test the connection
echo "This is BHARATH WEB Server" > /var/www/html/index.html

Edited nginx.conf as below  included a line as below include /etc/nginx/vhost.d/*.conf;

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   15;
    types_hash_max_size 2048;

   client_body_buffer_size 10k;
   client_header_buffer_size 1k;
   client_max_body_size 18m;
   large_client_header_buffers 2 1k;
   client_body_timeout 12;
   client_header_timeout 12;
   send_timeout 10;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/vhost.d/*.conf;
}
```
And the vhost.d/default.conf file is as below...

```
server {
        listen       80 default_server;
        server_name _;

        location / {
            root   /var/www/html;
            index  index.html index.htm;
        }
        error_page  404              /404.html;
       location = /404.html {
             root /usr/share/nginx/html;
            }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
```

` Afer changing the above config restart nginx and hit the URL/web.bharathkumarraju.com in browser as below `


![NginxLoaded](screen3.png?raw=true)


##  Vhost files (Virtual Hosts) to answer multiple domains

```
   Add  an entry in /etc/hosts as an local_IP and domainname as you wish
  
   [root@ansible3 html]# cat /etc/hosts
   127.0.0.1   localhost localhost.localdomain localhost4 ansible3.bharath.com
   ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
   10.0.3.127 test.domainvhost.com
 
    mkdir -p /var/www/html/testdomainvhost
    echo "test.domainvhost.com" > /var/www/html/testdomainvhost/index.html
    cd /etc/nginx/vhost.d/
    vim test.domainvhost.com.conf
    server {
      listen 80;

      root /var/www/html/testdomainvhost;
      index index.html index.htm index.php;
      server_name test.domainvhost.com mydomainvhost;
  }

After adding reload the nginx  and hit the URL as below and commandline and in Browser!!!

  elinks http://test.domainvhost.com
  
```
![domainvhos](screen4.png?raw=true)



## UPSTREAM Module in NGINX

Upstream module is used to define Groups of servers that we can reference and manage using no.of keywords with in our vhost coniguration like our proxy_pass fastcgi_pass, uwsgi_pass, scgi_pass, and memcached_pass directives.

`UPSTREAM Basically used as a reference lets say if we are running another HTTP server in the port 8888 what we do is ask our nginx upstream module to proxy pass the request to the 8888 PORT how to achieve this is very simple follow as below`



* We will run a Static NODE JS HTTP server on a particular port and ASK our nginx UPSTREAM module to serve that URL and PORT effectively.

```
Go to cd /var/www/html and create  directory called node and copy the below node js statch HTTP server code and run as below

```

![runnningnodejs](screen6.png?raw=true)

##### SO we will RUN nodeJS in 8888 PORT and we ask nginx upstream module to proxy the default web request to this

` Just do yum install -y npm`

```
The static NODE JS HTTP server code as below

ot@ansible3 nginx]# cat ../node/server.js
var http = require("http"),
    url = require("url"),
    path = require("path"),
    fs = require("fs")
    port = process.argv[2] || 8888,
    mimeTypes = {
  '.ico': 'image/x-icon',
    '.html': 'text/html',
    '.js': 'text/javascript',
    '.json': 'application/json',
    '.css': 'text/css',
    '.png': 'image/png',
    '.jpg': 'image/jpeg',
    '.wav': 'audio/wav',
    '.mp3': 'audio/mpeg',
    '.svg': 'image/svg+xml',
    '.pdf': 'application/pdf',
    '.doc': 'application/msword',
    '.eot': 'appliaction/vnd.ms-fontobject',
    '.ttf': 'aplication/font-sfnt'
  };

http.createServer(function(request, response) {

  var uri = url.parse(request.url).pathname,
      filename = path.join(process.cwd(), uri);

  fs.exists(filename, function(exists) {
    if(!exists) {
      response.writeHead(404, { "Content-Type": "text/plain" });
      response.write("404 Not Found\n");
      response.end();
      return;
    }

    if (fs.statSync(filename).isDirectory())
      filename += '/index.html';

    fs.readFile(filename, "binary", function(err, file) {
      if(err) {
        response.writeHead(500, {"Content-Type": "text/plain"});
        response.write(err + "\n");
        response.end();
        return;
      }

      var mimeType = mimeTypes[filename.split('.').pop()];

      if (!mimeType) {
        mimeType = 'text/plain';
      }

      response.writeHead(200, { "Content-Type": mimeType });
      response.write(file, "binary");
      response.end();
    });
  });
}).listen(parseInt(port, 10));

console.log("Static file server running at\n  => http://localhost:" + port + "/\nCTRL + C to shutdown");
Save this run this code as below

```

to run node js script node server.js &

![nodejs](screen5.png?raw=true)


```
 In order to server this node JS reqest via Nginx upstream module lets create a vhost file and do this 
  
cat /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 ansible3.bharath.com
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.0.3.127 test.domainvhost.com
10.0.3.127 test.mynode.local mynode

We have created a new domain name called test.mynode.local in /etc/hosts

cd /etc/nginx/vhost.d

vim /etc/nginx/vhost.d/test.mynode.lcoal.conf

upstream  mynode {
     server localhost:8888;
}

server {
     server_name test.mynode.lcoal;
     location / {
                 proxy_pass http://mynode;
}
}

In above configuration we are asking upstream to serve / request 
to the node js localhost 8888 PORT here comes the use of UPSTREAM module very effectively


```

```
Now we can serve vhost test.mynode.local to the 
  nodejs server that is to port 8888 we used upstream

So if we call elinks http://test.mynode.lcoal ...
It would effectively call the proxy_pass http://mynode; 
And mynode refers to the nodejs http server 
```

![runnningnodejsATLAST](screen7.png?raw=true)


## LoadBalance Using upstream Diective across multiple web servers

```
LoadBalance Across multiple web servers

And those web servers can be multiple services listening on same physical or virtual machine
or  those can be references of other servers or domains on one or more ports and will loadbalance through round robin

Lets setup two node nodeJS system so that we can loadbalance using our upstream directive


[root@ansible1 nginx]# cat vhost.d/test.mynode.local.conf
upstream  mynode {
     server localhost:8888  weight=1;
     server localhost:8889  weight=4;
}

server {
     server_name test.mynode.lcoal;
     location / {

                 proxy_pass http://mynode;
}
}


Just RUN another node JS with  port 8889 after adding the above upstream directive nginx will loadbalance your request as below now
you can also put weight condition  for 8889 will show you 4 times were as 8888 for 1 time


[root@ansible1 nginx]# cd html/
[root@ansible1 html]# cd node/
[root@ansible1 node]# ls -rtlh
total 16K
-rw-r--r--. 1 bharath bharath 1.7K Apr 15 10:30 server.js
-rw-r--r--. 1 root    root      13 Apr 15 11:28 index.html
-rw-r--r--. 1 root    root      13 Apr 15 11:28 index2.html
-rw-r--r--. 1 bharath bharath 1.7K Apr 15 11:29 server2.js
[root@ansible1 node]# cat index*.html
test server2
test server1
[root@ansible1 node]#


```

###### Loadbalance between two node JS ports

![loadbalanceupstream](screen8.png?raw=true)



# SSL Certificate Management

```

1. How to generate our server key, 
2. How to generate the certificate signing request (for exchange with a third party for a valid certificate) 



3. How to Generate self signed a certificate ,  In this you will get cross mark in browser HTTPS ,
   you can use this for NON-PROD and testing environments.

### How to exchange our certicate with independent third party certificate repositories like godaddy semantic




```








# SELINUX and SEPOLICY to DISABLE the SELINUX on particular PORT

```
getenforce

setenforce 0

yum install -y setroubleshoot-server selinux-policy-devel

sepolicy network -t http_port_t
http_port_t: tcp: 8888,8080,80,81,443,488,8008,8009,8443,9000

semanage port -a -t http_port_t -p tcp 8080
semanage port -m -t http_port_t -p tcp 8080

semanage port -a -t http_port_t -p tcp 8888
semanage port -m -t http_port_t -p tcp 8888

sepolicy network -t http_port_t
http_port_t: tcp: 8888,8080,80,81,443,488,8008,8009,8443,9000

```

 
