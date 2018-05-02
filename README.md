# Mac iOS + Nginx + Homebrew + MariaDB + php@7.0 + php@7.1 + Magento 2
How To create local env using Mac iOS + Nginx + Homebrew + php@7.0 + php@7.1

Due to Homebrew/php tap being deprecated at the end of March 2018, and the moving of all PHP formulas to Homebrew/core I was struggling to find a tutorial that will explain how to create local environment with all this changes.

## 1. Install Xcode
If you don't have XCode installed, do this first because it will be used by Homebrew
```
$ xcode-select --install
```

## Install Homebrew
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Follow terminal prompts and after is finished the easiest way to make sure all is good is to type:
```
$ brew -v
Homebrew 1.6.2
Homebrew/homebrew-core (git revision efb00; last commit 2018-04-27)
```

Just to be sure run:
```
$ brew doctor
```
It will instruct you how to fix potential problems.

**Note** *After you run this you will see message like this:*
```
Please note that these warnings are just used to help the Homebrew maintainers
with debugging if you file an issue. If everything you use Homebrew for is
working fine: please don't worry or file an issue; just ignore this. Thanks!
```
And if after that you only see few link issues and not some major issues, just wait to finish full installation and if all is running fine no need to do anything.


## Install NGINX

```
$ brew install nginx
```
After installation you will be given path to nginx.conf file:
```
 /usr/local/etc/nginx/nginx.conf
 ```
 To start, restart and stop nginx you will use:
 ```
 $ brew services start nginx
 $ brew services restart nginx
 $ brew services stop nginx
 ```
 In case you need to be sure that it started,restarted or stopped use sudo:
 ```
 $ sudo brew services start nginx
 ```
 
 Edit **/usr/local/etc/nginx/nginx.conf**, change:
 ```
 #user nobody;
 ```
 to your username (best one you are using to log to your Mac)
 ```
 user <your username> staff;
 ```
 On Mac login user is associated to a group **staff** and we are going to keep that. This is important if you are working with a code like Magento2 Commerce Cloud, that needs to have defined file's owner and group.
 
 Further down change:
 ```
 Listen 8080
 ```
 to 
 ```
 Listen 80
 ```
 If you are going to use Nginx and Apache on the same machine just follow this tuts to the end. We are going to setup Apache to listen 8080.
 
 After this change:
 ```
 location / {
    root   html;
    index  index.html index.htm;
}
```
to
```
location / {
    root   /Users/<username>/Nginx;
    index  index.html index.htm;
}
```
Save and close nginx.conf file and go to create a new folder in /Users/<your username>/ call it Nginx. We are creating a directory that we just defined in nginx.conf file as a root. 
```
$ mkdir -p /Users/<your username>/Nginx
```
This is folder where we are going to keep projects running on Nginx.
 
**Define locations**
This is equal to Apache vhost file. We are going to add our file to:
```
$ vim /usr/local/etc/nginx/servers/_localhost.conf
```
And we can add this:
```
upstream fastcgi_backend {
     server  127.0.0.1:9070;
     server  127.0.0.1:9071;
}

 server {
     listen 80;
     server_name <name of your test site>;
     set $MAGE_ROOT /Users/ivagarfitt/Nginx/<site directory>;
     include /Users/ivagarfitt/Nginx/<site directory>/nginx.conf.sample;
 }

server {
     listen 80;
     server_name <name of your test site2>;
     set $MAGE_ROOT /Users/ivagarfitt/Nginx/<site2 directory>;
     include /Users/ivagarfitt/Nginx/<site2 directory>/nginx.conf.sample;
 }
 ```
 
 This part:
 ```
 upstream fastcgi_backend {
     server  127.0.0.1:9070;
     server  127.0.0.1:9071;
}
```
Defines different port per PHP version. So for php@7.0 port is 9070, for php@7.1 is 9071 and so on. You can see this after we install different PHP versions.

It is very important to include path to nginx.conf.sample file, set in magento root directory. Without this connection Magento will not work with Nginx server.

# Install PHP@7.0

```
$ brew install php@7.0
```

**Note** *You no longer have to unlink each version between installing PHP versions as they are not linked by default*

Open /usr/local/etc/php/7.0/php-fpm.d/www.conf
```
$ vi /usr/local/etc/php/7.0/php-fpm.d/www.conf
```
Find and edit or uncomment or just add:
```
user = <your username>
group = staff
```
and set listen port to 9070:
```
listen = 127.0.0.1:9070
```
and
```
listen.group = staff
listen.mode = 0660
```
and uncomment, edit or add:
```
env[HOSTNAME] = $HOSTNAME
env[PATH] = /usr/local/bin:/usr/bin:/bin
env[TMP] = /tmp
env[TMPDIR] = /tmp
env[TEMP] = /tmp
```

# Install PHP@7.1

```
$ brew install php@7.1
```

**Note again** *You no longer have to unlink each version between installing PHP versions as they are not linked by default*

Open /usr/local/etc/php/7.1/php-fpm.d/www.conf
```
$ vi /usr/local/etc/php/7.1/php-fpm.d/www.conf
```
Find and edit or uncomment or just add:
```
user = <your username>
group = staff
```
and set listen port to 9071:
```
listen = 127.0.0.1:9071
```
and
```
listen.group = staff
listen.mode = 0660
```
and uncomment, edit or add:
```
env[HOSTNAME] = $HOSTNAME
env[PATH] = /usr/local/bin:/usr/bin:/bin
env[TMP] = /tmp
env[TMPDIR] = /tmp
env[TEMP] = /tmp
```

Let's switch back to the PHP@7.0 version now:
```
$ brew unlink php@7.1 && brew link --force --overwrite php@7.0
```
Test like:
```
$ php -v
PHP 7.0.29 (cli) (built: Apr 12 2018 03:15:13) ( NTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
    with Zend OPcache v7.0.29, Copyright (c) 1999-2017, by Zend Technologies
    with Xdebug v2.6.0, Copyright (c) 2002-2018, by Derick Rethans
```

# Restart NGINX and PHP
```
$ brew services restart nginx
```
Use sudo if you have to. Then restart PHP.
```
$ brew services restart php@7.0
```
Again use sudo if you have to.

# Install MariaDB

```
$ brew update
$ brew install mariadb
```
After installation is finished you can as advised start service with:
```
$ brew service start mariadb
```

This is a point where I like to download Sequel Pro (it is free) and to create a new connection using **Socket** option.
You don't need to change any settings.
But if you want to secure your database then run this:
```
$ /usr/local/bin/mysql_secure_installation
```
Just follow and answer all the prompts. This will set you up with a user and password.

