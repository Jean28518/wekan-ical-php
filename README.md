# wekan-ical-php

Calendar Synchronisation for Wekan. Supports single ical files or webcal sync.
Original Repository: [ViOffice/wekan-ical-php](https://codeberg.org/ViOffice/wekan-ical-php)

## ⚠️ WARNING: This Project is currently vunlarable to SQL-Injections ⚠️

## How to run in productions

For ubuntu or debian based systems, you can use the following commands to install:

```bash
sudo -i
# LAMP-stack
apt install caddy mariadb-server php-fpm php

# PHP modules
apt install php-curl php-mysql

# 3rdparty libraries
apt install composer

cd /var/www/
git clone https://github.com/Jean28518/wekan-ical-php.git
cd wekan-ical-php

cd libs/
chmod +x ./install_all.sh
sudo -u www-data ./install_all.sh

cd ..
cp conf/common.php.sample conf/common.php
vim conf/common.php 
# Adjust domains, and database settings

mysql
CREATE DATABASE wekanical;
USE wekanical;
CREATE TABLE wekanical (
    username CHAR(100),
    token CHAR(100),
    expire BIGINT,
    ical BIGINT);
CREATE USER wekanical@localhost IDENTIFIED BY 'your-secure-pw';
GRANT ALL PRIVILEGES ON wekanical.* to wekanical@localhost;
FLUSH PRIVILEGES;
QUIT;

vim /etc/caddy/Caddyfile 
# Insert the following entry and adjust the domain
wekanical.example.org {
  root * /var/www/wekan-ical-php/
  file_server

  php_fastcgi unix//var/run/php/php-fpm.sock {
  }

  header {
    Strict-Transport-Security max-age=31536000; # enable HSTS
  }

  #redir /.well-known/carddav /remote.php/dav 301
  #redir /.well-known/caldav /remote.php/dav 301

  @forbidden {
    path /conf/*
    path /README
    path /libs/*
  }

  respond @forbidden 404
}

systemctl restart caddy
```

## Deployment

Use the sample configuration `conf/common.php.sample` to create your
configuration file:
```
cp conf/common.php.sample conf/common.php
vi conf/common.php
```

Create a MariaDB/MySQL database with the credentials supplied in the
`common.php` config file:
```
CREATE DATABASE name_of_db;

USE name_of_db;

CREATE TABLE name_of_table (
    username CHAR(100),
    token CHAR(100),
    expire BIGINT,
    ical BIGINT)

CREATE USER name_of_user@localhost IDENTIFIED BY 'your-secure-pw';

GRANT ALL PRIVILEGES ON name_of_db.* to name_of_user@localhost;

FLUSH PRIVILEGES;
```

## Version-Upgrade

If you are running wekan-ical-php straight from `main` branch:

```
git pull
```

If you are running from a specific release:

```
git checkout main
git pull
git checkout 0.0.2
```
Either way, please take a look at the changelog from last commits or releases
and update your configurations and translations in `conf/` accordingly.

## Maintainers

* [Jan Weymeirsch](https://jan.weymeirs.ch)
    * Contact: [dev-AT-vioffice-DOT-de](mailto:dev<AT>vioffice<DOT>de)

## Contribute

Any pull requests or suggestions are welcome on the main repository at
<https://codeberg.org/ViOffice/wekan-ical-php>, the Github-Mirror at
<https://github.com/ViOffice/wekan-ical-php> or via [e-mail to the
maintainers](#maintainers).

Please make sure, your changes are
[REUSE-compliant](https://git.fsfe.org/reuse/tool)

## License

Copyright (C) 2021 [Weymeirsch und Langer GbR](mailto:dev<AT>vioffice<DOT>de)

See Licenses [here](LICENSES).
