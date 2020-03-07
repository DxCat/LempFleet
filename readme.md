# :ship: LempFleet
Dead simple LEMP multi-site docker skeleton :skull:

Sometimes, you would want to share a single server for multiple websites, but you still want to use docker.
Maybe you just want to have a much simpler docker setup for your development, and you dont want to have multiple instances of 
mysql, nginx, and having to build/rebuild tons of images.

Well, no matter what your reason is. Here's a very simple, drop-in, multi-site setup for docker on a LEMP stack.

**Laravel 6.0 support ready out-of-the-box**

# Stacks
1) Linux Alpine (php:7.3-fpm-alpine)
    - Installed packages : `libpng-dev libzip-dev zlib-dev unzip zip git composer`
    - Installed php extensions : `gd pdo_mysql mysqli mbstring zip exif pcntl`
2) NGINX (nginx:alpine)
3) Mysql (mysql:latest)

# Setting up
1) Clone this repository
```bash
$ git clone https://github.com/askaoru/LempFleet docker
```
2) Go into the directory and start the containers
```bash
$ cd docker && docker-compose up -d
```
3) That's all! You should be able to see that the containers are running and exec into them!  

To check for running services run
```bash
$ docker-compose ps

    Name                  Command              State                    Ports
-----------------------------------------------------------------------------------------------
applications   docker-php-entrypoint php-fpm   Up      9000/tcp
database       docker-entrypoint.sh mysqld     Up      0.0.0.0:3306->3306/tcp, 33060/tcp
webserver      nginx -g daemon off;            Up      0.0.0.0:443->443/tcp, 0.0.0.0:80->80/tcp
```

To access the services container run
```bash
$ docker exec -it applications /bin/sh
$ docker exec -it database /bin/sh 
$ docker exec -it webserver /bin/sh
```

# Adding a project
It's very simple!
1) Add your project into the app folder
Example
```
- app
  - project1 <-- here
    - index.php
  - project2 <-- and here
    - index.php
- mysql
- nginx
- php
- docker-compose.yml
```
2) Create nginx conf file for your projects
```
- app
- mysql
- nginx
  - project1.conf <-- this
  - project2.conf <-- and this
- php
- docker-compose.yml
```
  with the following contents example for each of them.
```Nginx
server {
    listen 80;
    index index.php index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    
    server_name project1.test; # Change accordingly
    root /app/project1; # Change accordingly
    
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        gzip_static on;
    }
}
```
3) And done! All you need to do now is just to restart the services
```bash
$ docker-compose down && docker-compose up -d
```

# That's all!
Feel free to open an issue if you have any question and pull requests are appreciated!

For more examples on commonly used container, check it out [here](https://github.com/askaoru/LempFleet/blob/master/examples.md).

