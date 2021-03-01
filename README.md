### Install Nginx on CentOS 7 or  RHEL 7 — 

Site address : https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#source-releases

[root@localhost ~]# wget https://nginx.org/download/nginx-1.10.1.tar.gz

#### Extract , do cd inside directory and perform below 

[root@localhost ~]# ./configure

[root@localhost ~]# make

[root@localhost ~]# sudo make install

By default, NGINX will be installed in /usr/local/nginx


### Start/stop/restart nginx server — 

[root@localhost ~]# sudo systemctl enable nginx

[root@localhost ~]# sudo systemctl start nginx

[root@localhost ~]# sudo systemctl status nginx


### Open port 80 and 443 using firewall-cmd — 

[root@localhost ~]# sudo firewall-cmd --permanent --zone=public --add-service=http

[root@localhost ~]# sudo firewall-cmd --permanent --zone=public --add-service=https

[root@localhost ~]# sudo firewall-cmd --reload


### Test it — 

[root@localhost ~]# sudo ss -tulpn

[root@localhost ~]# ip a

You can also use the curl command to get same info using the cli

[root@localhost ~]# curl http://172.16.0.68

[root@localhost ~]# curl -I http://172.16.0.68

http://server_domain_name_or_IP/


### Set Up Nginx Server Blocks on CentOS 7 (Virtualhost) — 

#### Create the Directory Structure — 

[root@localhost ~]# sudo mkdir -p /var/www/example.com/html

[root@localhost ~]# sudo mkdir -p /var/www/example2.com/html


#### Grant Permissions — 

[root@localhost ~]# sudo chown -R $USER:$USER /var/www/example.com/html

[root@localhost ~]# sudo chown -R $USER:$USER /var/www/example2.com/html


#### Create Demo Pages for Each Site — 

[root@localhost ~]# nano /var/www/example.com/html/index.html

```
<html>
  <head>
    <title>Welcome to Example.com!</title>
  </head>
  <body>
    <h1>Success! The example.com server block is working!</h1>
  </body>
</html>
```

[root@localhost ~]# cp /var/www/example.com/html/index.html /var/www/example2.com/html/index.html

[root@localhost ~]# nano /var/www/example2.com/html/index.html

```
<html>
  <head>
    <title>Welcome to Example2.com!</title>
  </head>
  <body>
    <h1>Success! The example2.com server block is working!</h1>
  </body>
</html>
```

#### Create New Server Block Files — 

[root@localhost ~]# sudo mkdir /etc/nginx/sites-available

[root@localhost ~]# sudo mkdir /etc/nginx/sites-enabled

[root@localhost ~]# sudo nano /etc/nginx/nginx.conf (Add these lines to the end of the http {} block)

```
include /etc/nginx/sites-enabled/*.conf;
server_names_hash_bucket_size 64;
```

#### Create the First Server Block File — 

[root@localhost ~]# sudo cp /etc/nginx/conf.d/default.conf /etc/nginx/sites-available/example.com.conf

[root@localhost ~]# sudo nano /etc/nginx/sites-available/example.com.conf

```
server {
    listen  80;

    server_name example.com www.example.com;

    location / {
        root  /var/www/example.com/html;
        index  index.html index.htm;
        try_files $uri $uri/ =404;
    }

    error_page  500 502 503 504  /50x.html;
    location = /50x.html {
        root  /usr/share/nginx/html;
    }
}
```

#### Create the Second Server Block File — 

[root@localhost ~]# sudo cp /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-available/example2.com.conf

[root@localhost ~]# sudo nano /etc/nginx/sites-available/example2.com.conf

```
server {
    listen  80;

    server_name example2.com www.example2.com;

    location / {
        root  /var/www/example2.com/html;
        index  index.html index.htm;
        try_files $uri $uri/ =404;
    }

    error_page  500 502 503 504  /50x.html;
    location = /50x.html {
        root  /usr/share/nginx/html;
    }
}
```

#### Enable the New Server Block Files — 

[root@localhost ~]# sudo ln -s /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-enabled/example.com.conf

[root@localhost ~]# sudo ln -s /etc/nginx/sites-available/example2.com.conf /etc/nginx/sites-enabled/example2.com.conf


[root@localhost ~]# sudo systemctl restart nginx


#### Set Up Local Hosts File (Optional) — 

[root@localhost ~]# sudo nano /etc/hosts

127.0.0.1   localhost
127.0.1.1   guest-desktop
server_ip_address example.com
server_ip_address example2.com


#### Test Your Results — 

http://example.com


.
  
**@ By — Anup Kumar Mondal**
