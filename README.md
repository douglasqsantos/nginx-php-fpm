# WebServer Nginx and PHP-FPM

[![N|Solid](https://linuxsolutions.xyz/linuxSolution.jpeg)](https://linuxsolutions.xyz)

Image ready to build your web server with Nginx and PHP-FPM.

Current versions composed in this image;
  - [Nginx](http://nginx.org/en/download.html) 1.19.3
  - [PHP-FPM](https://www.php.net/manual/en/install.fpm.php) 7.4.x
  - [Composer](https://getcomposer.org/doc/00-intro.md) 1.10.7
  - [WP-CLI](https://developer.wordpress.org/cli/commands/)

# Features!

> Image based on Debian Buster Slim.

## With WordPress support

* Meets the required requirements! [WordPress](https://make.wordpress.org/hosting/handbook/handbook/server-environment/#php-extensions)

### What do you need to know!

* Default Nginx Welcome Directory:

```sh
$ /var/www/html
```

* Default Nginx configuration:

```sh
$ /etc/nginx/conf.d/default.conf
```

* General site customization settings and possibilities to add other sites as well:

```sh
$ /etc/nginx/nginx.conf
```

* Already set by default in nginx.conf:

```sh
$ include /etc/nginx/conf.d/*.conf;
$ include /etc/nginx/sites-enabled/*.conf;
$ include /etc/nginx/upstream/*.conf;
```

* Support ACL HTTP authentication

```sh
$ include common/acl.conf;
```

The configuration file is located in /etc/nginx/htpasswd
You can generate credentials on this website: https://www.web2generators.com/apache-tools/htpasswd-generator
To the credentials defined by default:

```sh
# htpasswd: fuerzastudio | create-your-password
```

* Example to run the server.

```sh
$ docker run --name my-server -d -p 8080:80 fuerzastudio/nginx-php-fpm
```

* In the Browser:

```sh
127.0.0.1:8080 or your-ip-host:8080
```

* Referencing your project:

```sh
$ docker run --name my-server -d -p 8080:80 -v /var/www/project:/var/www/html fuerzastudio/nginx-php-fpm
```

* Docker Compose:

```sh
version: '3'

services:
  
  server-web:
    image: fuerzastudio/nginx-php-fpm
    container_name: webserver
    restart: always
    volumes:
      - ./custom-site.conf:/etc/nginx/conf.d/default.conf #Example below
      - /path/your-project/:/var/www/html
    ports:
      - '80:80'
      - '443:443'
    expose:
      - '22'
    networks: 
      dev:
        ipv4_address: 171.28.5.10
networks: 
  dev:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
      - subnet: 171.28.0.0/16
```

* Example configuration for your site by editing the file ***custom-site.conf***; 

```sh
server {
    listen   80; ## listen for ipv4; this line is default and implied
    listen   [::]:80 default ipv6only=on; ## listen for ipv6

    root /var/www/html;
    index index.php index.html index.htm;

    # Make site accessible from http://localhost/
    server_name your-domain.com www.your-domain.com; #Here my dear, you put your domain!

    # Disable sendfile as per https://docs.vagrantup.com/v2/synced-folders/virtualbox.html
    sendfile off;

    # Security - Hide nginx version number in error pages and Server header
    server_tokens off;

    # Add stdout logging
    error_log /dev/stdout info;
    access_log /dev/stdout;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to index.html
        try_files $uri $uri/ =404;
    }

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # pass the PHP scripts to FastCGI server listening on socket
    #
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

        location ~* \.(jpg|jpeg|gif|png|css|js|ico|xml)$ {
                expires           5d;
        }

    # deny access to . files, for security
    #
    location ~ /\. {
            log_not_found off;
            deny all;
    }

}
```

* Example with database, [MariaDB](https://hub.docker.com/_/mariadb):

```sh
version: '3'

services:
  
  server-web:
    image: fuerzastudio/nginx-php-fpm
    container_name: webserver
    restart: always
    volumes:
      - ./custom-site.conf:/etc/nginx/conf.d/default.conf #Example below
      - /path/your-project/:/var/www/html
    ports:
      - '80:80'
      - '443:443'
    expose:
      - '22'
    networks: 
      dev:
        ipv4_address: 171.28.5.10
    server-database:
    image: mariadb
    container_name: database
    restart: always
    volumes:
      - ./data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=passroot
      - MYSQL_USER=example_usr              
      - MYSQL_PASSWORD=example_pass
      - MYSQL_DATABASE=example_db               
    ports:
      - '3306:3306'
    networks: 
      dev:
        ipv4_address: 171.28.5.11
networks: 
  dev:
    driver: bridge
    driver_opts:
      com.docker.network.enable_ipv6: "false"
    ipam:
      driver: default
      config:
      - subnet: 171.28.0.0/16
```

---
* Hub Docker: [See Now](https://hub.docker.com/r/fuerzastudio/nginx-php-fpm)
* GitHub Repository: [See Now](https://github.com/fuerzastudio/nginx-php-fpm)
* Project website: [See Now](https://fuerzastudio.com)
---

License
----

GNU General Public License v3.0


**Free Software, Hell Yeah!**
