---
title: Yii2学习笔记『一』
date: 2016-08-12 18:12:18
categories: PHP
tags: yii2
---

为了避免在别人说『PHP是世界上最好的语言』的时候插不上话，于是学起了PHP~
自从两年多前告别Java之后，再次接触后端语言，感觉很新鲜。ps虽然PHP在大多数工程师眼里算不得什么后端语言（啊？什么？我还做过Java……惊恐脸(´･_･`)

> 目录
> 1. Mac OS使用brew安装Nginx、MySQL、PHP-FPM的LAMP开发环境
> 2. 安装Composer
> 3. 通过 Composer 安装Yii

<!-- more -->


-----


### 1. Mac OS使用brew安装Nginx、MySQL、PHP-FPM的LAMP开发环境
    [参考文档](http://tabalt.net/blog/install-nginx-mysql-php-fpm-by-brew-on-mac/)

### 2. 安装Composer

```
$ curl -s http://getcomposer.org/installer | php
$ mv composer.phar /usr/local/bin/composer
$ composer self-update 
```

### 3. 通过 Composer 安装Yii

* 第一个命令安装 Composer asset plugin,它是通过 Composer 管理 bower 和 npm 包所必须的,此命令全局生效,一劳永逸。
* 第二个命令将 Yii 安装在名为 basic 的目录中,你也可以随便选择其他名称。*一般是在workspace目录下执行，basic 为你的项目名称，如果是第一次安装，过程会灰常慢，耐心~ 实在不行的话可以找其他方式，比如镜像or归档文件*


```
$ composer global require "fxp/composer-asset-plugin:~1.1.1"
$ composer create-project --prefer-dist yiisoft/yii2-app-basic basic
```

 * 第一个命令刚开始写的版本不对，报下面的错，供参考
```
Your requirements could not be resolved to an installable set of packages.

  Problem 1
    - Installation request for fxp/composer-asset-plugin 1.0.0 -> satisfiable by fxp/composer-asset-plugin[v1.0.0].
    - fxp/composer-asset-plugin v1.0.0 requires composer-plugin-api 1.0.0 -> no matching package found.

Potential causes:
 - A typo in the package name
 - The package is not available in a stable-enough version according to your minimum-stability setting
   see <https://getcomposer.org/doc/04-schema.md#minimum-stability> for more details.

Read <https://getcomposer.org/doc/articles/troubleshooting.md> for further common problems.

Installation failed, reverting ./composer.json to its original content.
```


 * 推荐使用的 nginx 配置

```
server {
    charset utf-8;
    client_max_body_size 128M;
    
    listen 80; ## 监听 ipv4 上的 80 端口
    # listen [::]:80 default_server ipv6only=on; ## 监听 ipv6 上的 80 端口
    
    server_name mysite.local; 
    root /path/to/basic/web; 
    index index.php;
    
    access_log /path/to/basic/log/access.log main; 
    error_log /path/to/basic/log/error.log;
    
    location / {
        # 如果找不到真实存在的文件,把请求分发至 index.php 
        try_files $uri $uri/ /index.php?$args;
    }
    
    # 若取消下面这段的注释,可避免 Yii 接管不存在文件的处理过程(404) 
    #location ~ \.(js|css|png|jpg|gif|swf|ico|pdf|mov|fla|zip|rar)$ {
    #   try_files $uri =404;
    #}
    #error_page 404 /404.html;
    
    location ~ \.php$ {
        include fastcgi.conf;
        fastcgi_pass 127.0.0.1:9000; 
        #fastcgi_pass unix:/var/run/php5-fpm.sock; 
        try_files $uri =404;
    }
    location ~ /\.(ht|svn|git) { 
        deny all;
    } 
}
```



> 使用该配置时,你还应该在 php.ini 文件中设置 cgi.fix_pathinfo=0 ,能避免掉很多不必要的 stat() 系 统调用。
还要注意当运行一个 HTTPS 服务器时,需要添加 fastcgi_param HTTPS on; 一行,这样 Yii 才能正确 地判断连接是否安全。
>

*截至到这里，如果你的 nginx server 也配置好了，就可以访问刚才创建好的项目了，你将会看到 Yii 的欢迎界面，Congratulations!*


