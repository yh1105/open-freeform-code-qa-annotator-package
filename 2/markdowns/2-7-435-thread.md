
# Post \#57048428 [Link](https://stackoverflow.com/questions/57048428/)

## E: Package 'mysql-client' has no installation candidate in php-fpm image build using docker compose

**Vote**: 137 (37/702) **Views**: 76341 (112/702) 

**Internal ID** \#2-7-435

Created at 2019-07-15 23:52:55

Tags: `laravel` `docker` `docker-compose` `dockerfile`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

Im fairly new to docker and so im trying to learn more about it using a laravel project, im following this tutorial:
[https://www.digitalocean.com/community/tutorials/how-to-set-up-laravel-nginx-and-mysql-with-docker-compose](https://www.digitalocean.com/community/tutorials/how-to-set-up-laravel-nginx-and-mysql-with-docker-compose)
Ive adjusted the Dockerfile a bit from what the tutorial has but even the tutorial file causes the same result.
```
FROM php:7.3-fpm

# Copy composer.lock and composer.json
COPY composer.lock composer.json /var/www/
# Install dependencies
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash - && \
    apt-get update && apt-get install -y mysql-client \
    

RUN npm install -g npm

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*


# Install extensions
RUN docker-php-ext-install pdo pdo_mysql

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Change current user to www
USER www
# Set working directory
WORKDIR /var/www
# Expose port 9000 and start php-fpm server
EXPOSE 9000

CMD ["php-fpm"]
```

But i keep getting the following error when i run `docker-compose up -d`:
```
E: Package 'mysql-client' has no installation candidate
ERROR: Service 'app' failed to build: The command '/bin/sh -c curl -sL https://deb.nodesource.com/setup_10.x | bash - &&     apt-get update && apt-get install -y mysql-client     nodejs     build-essential     vim     git     curl' returned a non-zero code: 100
```

Am i missing something?
I expected this to work since i am running `apt-get update` before installing `mysql-client`.
Thanks.


----------
        
## Answer \#0

**Accepted** Vote: 243

Created at 2019-07-16 02:37:21

------------

php:7.3-fpm now use Debian 10 (Buster) as its [base image](https://github.com/docker-library/php/blob/ccbe53516fa2fd2d3b0ba680b83148b443a6ccf1/7.3/buster/fpm/Dockerfile) and Buster ships with MariaDB, so just replace `mysql-client` with `mariadb-client` should fix it.


------------
    
    
## Answer \#1

 Vote: 244

Created at 2019-08-14 06:34:17

------------

If you still want to use the mysql client, it's called `default-mysql-client` now.


------------
    
    
## Answer \#2

 Vote: 3

Created at 2019-09-12 10:37:54

------------

`php:7.2-apache` triggers the error as well, but I resolve it using `php:7.2.18-apache`


------------
    
    
## Answer \#3

 Vote: -1

Created at 2022-05-04 18:48:04

------------

it worked for me: `sudo apt-get update && apt-get install -y git curl libmcrypt-dev default-mysql-client`
or alternatively `apt-cache search mysql-server`
find out your servers then `sudo apt-get install default-mysql-server default-mysql-server-core mariadb-server-10.6 mariadb-server-core-10.6`
in my case it was the above codes


------------
    
    