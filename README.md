# Nginx  

<img src="3630px-Nginx_logo.png">

The Most powerful and highly used web server in the world .
Almost every third website runs on nginx.

## Installing nginx on RHEL 7.X linux 

[install link] ('https://www.nginx.com/resources/wiki/start/topics/tutorials/install/')

## creating yum repo 

```
[nginx]
name=nginx repo
baseurl=https://nginx.org/packages/rhel/$releasever/$basearch/
gpgcheck=0
enabled=1

```

## Installing nginx 

```
yum install nginx -y
```

## Start nginx 

```
systemctl start nginx 
systemctl enable nginx 
```

## checking some deep dive information about nginx 

### Version 

```
[root@ip-172-31-22-247 ~]# nginx -v
nginx version: nginx/1.18.0

```

### Detailed information about 

```
[root@ip-172-31-22-247 ~]# nginx -V
nginx version: nginx/1.18.0
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-39) (GCC) 
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie'

```


### Reload nginx service gracefully 

```
[root@ip-172-31-22-247 ~]# nginx -s reload
```

### Dump entire Nginx configuration files 

```
[root@ip-172-31-22-247 ~]# nginx -T 
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
# configuration file /etc/nginx/nginx.conf:

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;


```

## Configure SSL certificates for NGInx 

### create self sign certificates 

```
cd /etc/ssl/
mkdir nginx
openssl req -x509 -nodes -days 365  -newkey rsa:2048 -keyout private.key -out  pub.crt
```

### configuration file for SSL 

```
[root@ip-172-31-22-247 nginx]# cat /etc/nginx/conf.d/default.conf 
server {
    listen       80;
    listen    443 ssl;
    server_name  localhost;
    ssl_certificate /etc/ssl/nginx/pub.crt; 
    ssl_certificate_key /etc/ssl/nginx/private.key;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;

```

## NGinx

###
What is SSL Termination?
SSL termination means that NGINX Plus acts as the server-side SSL endpoint for connections with clients: it performs the decryption of requests and encryption of responses that backend servers would otherwise have to do. The operation is called termination because NGINX Plus closes the client connection and forwards the client data over a newly created, unencrypted connection to the servers in an upstream group. In release R6 and later, NGINX Plus performs SSL termination for TCP connections as well as HTTP connections.

###

## URL of nginx SSL termination 

['ssl termination']  ('https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-tcp/')

# Nginx WAF

###

NGINX Plus Release 12 and later supports the NGINX ModSecurity WAF. The NGINX ModSecurity WAF protects web applications against SQL Injection (SQLi), Remote Code Execution (RCE), Local File Include (LFI), cross‑site scripting (XSS), and many other attacks.

###


## Installing Developement tools 

```
yum groupinstall  "Development Tools"
yum install -y  geoip-devel  gperftools-devel  libcurl-devel  libxml2-devel  libxslt-devel  libgd-devel  lmdb-devel  openssl-devel  pcre-devel  perl-ExtUtils-Embed  yajl-devel  zlib-devel

 yum install http://mirror.centos.org/centos/7/os/x86_64/Packages/gd-devel-2.0.35-26.el7.x86_64.rpm
 
 ```
 
 ### cloning mode security module and compile it with nginx once
 
 ```
 cd /opt
 git clone --depth 1 -b v3/master https://github.com/SpiderLabs/ModSecurity 
 
 ```
 
 ## Compiling modules 
 
 ```
 cd Modsecurity
  git submodule init  # it generates warning then only
  
  ===
  cat /etc/environment 

LANG=en_US.UTF-8
LC_ALL=en_US.UTF-8

====

git submodule update
./build.sh
./configure
make
make install
```

## download modusecurity Nginx modules

```
cd /opt/
git clone --depth 1 https://github.com/SpiderLabs/ModSecurity-nginx.git
nginx -v # check version and download the matching tar file
wget http://nginx.org/download/nginx-1.18.0.tar.gz
tar xvzf nginx-1.18.0.tar.gz 
cd nginx-1.18.0
./configure --with-compat --add-dynamic-module=../ModSecurity-nginx
make modules
```

### adding modules in nginx.conf 

```
cd /opt/nginx-1.18.0
cp -v objs/ngx_http_modsecurity_module.so /etc/nginx/modules/

-----------
[root@ip-172-31-22-247 nginx-1.18.0]# cat /etc/nginx/nginx.conf 

user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


# Loading security modules 
load_module /etc/nginx/modules/ngx_http_modsecurity_module.so;

-----

```

### testing config 

```
nginx -t
```

## Now enable modusecurity with rules 

```
mkdir  /etc/nginx/modsecurity
cp -v /opt/ModSecurity/modsecurity.conf-recommended  /etc/nginx/modsecurity/modsecurity.conf
```

## chaning in configuration file

### in the line 230 

```
[root@ip-172-31-22-247 nginx-1.18.0]# grep -i audit  /etc/nginx/modsecurity/modsecurity.conf  -n
214:# -- Audit log configuration -------------------------------------------------
220:SecAuditEngine RelevantOnly
221:SecAuditLogRelevantStatus "^(?:5|4(?!04))"
224:SecAuditLogParts ABIJDEFHZ
227:# assumes that you will use the audit log only ocassionally.
229:SecAuditLogType Serial
230:SecAuditLog /var/log/nginx/modsec_audit.log
232:# Specify the path for concurrent audit logging.
233:#SecAuditLogStorageDir /opt/modsecurity/var/audit/

```


  
