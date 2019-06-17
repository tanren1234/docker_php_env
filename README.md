### 构建镜像
```
克隆dockerfile
git
clone
https://github.com/tanren1234/docker_php_env
```

###### php
>  docker build -t docker_php .
###### Nginx
>  docker build -t docker_nginx .

#### php
```
docker run --name  docker_php -v D:/docker_www/www:/docker/www -v D:/docker_www/log:/docker/log  -d php:filetest
```

#### nginx
```
docker run -d  -p 8084:80 --name docker_nginx -v D:/docker_www/www:/docker/www -v D:/docker_www/log:/docker/log -v D:/docker_php_env/nginx/conf.d:/etc/nginx/conf.d:ro --link docker_php:php docker_nginx:latest
```

##### 注意
```
docker logs docker_nginx
2019/06/17 08:19:24 [emerg] 1#1: open() "/docker/log/nginx/access.log" failed (2: No such file or directory)
nginx: [emerg] open() "/docker/log/nginx/access.log" failed (2: No such file or directory)
```
> 新建文件在D:\docker_www\log\nginx中

```
location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass   docker_php:9000;
    #fastcgi_pass  unix:/var/run/php-fpm.sock;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    include        fastcgi_params;
    try_files $uri =404;
}
```
> docker_php 为PHP容器的名称