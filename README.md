# PHP-FPM based on Debian

**Debian based implementation of [liteacz/php](https://github.com/liteacz/php) docker image**

## Content

- [About](#about)
- [Getting started](#getting-started)

## About

I'm mostly using liteacz/php in my projects, but sometimes there are services that requires usage of additional libraries which make small alpine image to grow extremely.

This image aspires to be debian copy of [liteacz/php](https://github.com/liteacz/php) providing necessary tools for implementation adittional services or tools.

The fpm image is meant to play well with [liteacz/nginx](https://github.com/liteacz/nginx), but it can be used by any other webserver that supports [fastcgi](https://en.wikipedia.org/wiki/FastCGI).

## Getting started

### Using FPM

Assuming the following project structure, here is simple `docker-compose.yml` example that shows how to use the **fpm** and [liteacz/nginx](https://github.com/liteacz/php/blob/7.4/github.com/liteacz/nginx) images together for local development.

```
myproject
├── app
│   └── MyController.php
├── docker-compose.yml
└── public
    └── index.php
```

**docker-compose.yml**

```yml
version: "3"
services:
  nginx:
    image: liteacz/nginx:1.19
    ports:
      - 8000:80
    volumes:
      - ./public:/var/www/public
    depends_on: 
      - php-fpm
    
  php-fpm:
    image: liteacz/php:8.0-fpm
      environment:
        FPM_ROOT_DIR: public
      volumes:
        - .:/var/www
```

Now when you call `docker-compose up -d` from within `myproject` folder you'll see something similar to:

```bash
$ myproject docker-compose up -d
Creating network "myproject_default" with the default driver
Creating myproject_php-fpm_1 ... done
Creating myproject_nginx_1   ... done
```

**Note**

> If you don't have php and nginx images pulled locally, the first run will take some time to pull them. The subsequent start (`docker-compose up -d`) will be faster.

Now verify that both nginx and php-fpm containers are up and running:

```bash
$ docker ps -a

CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                  NAMES
74bd21e8c4d8        liteacz/nginx:1.19    "docker-entrypoint n…"   12 seconds ago      Up 11 seconds       0.0.0.0:8000->80/tcp   myproject_nginx_1
1548c43f6aca        liteacz/php:8.0-fpm   "docker-entrypoint p…"   13 seconds ago      Up 11 seconds       9000/tcp               myproject_php-fpm_1
```

If you see "Up ..." within the "STATUS" column you should be able to navigate to [localhost:8000](http://localhost:8000) and see the "Hello dockerized PHP world!" message.
