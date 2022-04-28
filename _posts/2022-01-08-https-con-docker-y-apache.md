---
layout: post
title: HTTPS with Docker and Apache
subtitle: In this article we’ll explain how to configure Docker Compose to have a HTTPS website with Apache as server.
categories: docker
tags: [docker, apache]
---

Let's see, step by step, how configurate HTTPS with Docker Compose in a Apache server using a demo from the Yii framework.

[Here](https://github.com/EvaVillarrealGuzman/demo) you will find the final code.

## 1- Configure the development environment

In this first step, the goal is to configure the development environment machine to be able to do the tests with a fictitious domain at the end.

Suppose that the test will be done with the domain **test.com.ar**. So, edit the **hosts** file. The location of this file depends on the operating system: 

- In **Linux** you will find it in ```/etc/hosts```.
- In **Windows** you will find it in ```C:\Windows\System32\drivers\etc\hosts```.

This file is used by the operating system to map a IP address to a host name or domain name.

Add the following line and save:

```
127.0.0.1       test.com.ar
```

## 2- Clone the project from Github

We will use a [demo provided by the Yii framework](https://github.com/yiisoft/demo) to explain how to configure HTTPS with Docker and Apache. Fork the repository to your account. At the time of writing this article, I forked it from commit ```f19cc5898911d365310df664415c83e4ef8a8c85```.

## 3- Get the certificates

In order that our website work with the HTTPS protocol, it is necessary to have an SSL certificate installed, which will be in charge of encrypting the connections between our browser and the server where the website is hosted, so all the information exchanged between them is not accessible.

We won't delve into how generate this certificate, but you can get it free through [Let's Encrypt](https://letsencrypt.org/) or [StartSSL](https://startssl.com/).

Let's assume that you already have the certificate and it is called ```fullchain.pem``` and the private key is ```privkey.pem```. Add these files in a folder in the root path called ```ssl```. Also, add this folder in the ```.gitignore```, since this has sensitive information:

![](https://evavillarrealguzman.github.io/img/https-con-docker-y-apache/ssl-folder.png)

## 4- Edit Apache configuration files

Create a folder ```sites-available``` in the root of the project an add the files ```000-default.conf``` and ```default-ssl.conf```, with the following content to enable HTTPS in Apache:

**000-default.conf**

```
<Directory /app/public/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
</Directory>

<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /app/public

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

**default-ssl.conf**

```
<IfModule mod_ssl.c>
	<VirtualHost _default_:443>
		ServerAdmin webmaster@localhost

		DocumentRoot /app/public

		ErrorLog ${APACHE_LOG_DIR}/error.log
		CustomLog ${APACHE_LOG_DIR}/access.log combined

		SSLEngine on

        SSLCertificateFile /var/imported/ssl/fullchain.pem
		SSLCertificateKeyFile /var/imported/ssl/privkey.pem

		<FilesMatch "\.(cgi|shtml|phtml|php)$">
				SSLOptions +StdEnvVars
		</FilesMatch>
		<Directory /usr/lib/cgi-bin>
				SSLOptions +StdEnvVars
		</Directory>
	</VirtualHost>
</IfModule>
```

## [](#header-2)5- Configure Docker Compose

Create the ```Dockerfile``` file, which will define our Docker image base, as follows:

```
FROM yiisoftware/yii-php:8.0-apache

RUN a2enmod rewrite
RUN a2enmod ssl

RUN a2ensite default-ssl

EXPOSE 80
EXPOSE 443
```

As you can see above, we start from the original image of the project and then we enable the SSL to Apache.

You also have to edit the ```docker-compose.yml``` as following:

```yml
version: '3'

services:
    php:
        build:
          context: .
          dockerfile: Dockerfile
        working_dir: /app
        volumes:
            - ./:/app
            # host-volume for composer cache
            - ~/.composer-docker/cache:/root/.composer/cache:delegated
            - ./ssl:/var/imported/ssl
            - ./sites-available:/etc/apache2/sites-available
        ports:
            - '80:80'
            - '443:443'
```

In this way, the files that were created before and that allow the operation of HTTPS on the server are passed to the container.

## [](#header-2)6- Run the website

Finally, you need to fire up the containers running the command below:

```
docker-compose up -d
```

In case it is the first time you run, install the project libraries with composer:

```
docker-compose exec php composer install
```

Now you can test with the browser, accessing the site both HTTP and HTTPS:

![](https://evavillarrealguzman.github.io/img/https-con-docker-y-apache/http.png)

![](https://evavillarrealguzman.github.io/img/https-con-docker-y-apache/https.png)

