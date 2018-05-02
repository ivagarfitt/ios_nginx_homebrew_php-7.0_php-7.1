# Mac iOS + Nginx + Homebrew + MariaDB + php@7.0 + php@7.1
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
