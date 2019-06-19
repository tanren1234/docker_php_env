### 构建镜像
```
克隆dockerfile
git
clone
https://github.com/tanren1234/docker_php_env
```

####### D:/docker_www 数据卷挂载的目录

###### php
>  docker build -t docker_php .
###### Nginx
>  docker build -t docker_nginx .

#### php
```
docker run --name  docker_php -v D:/docker_www/www:/docker/www -v D:/docker_www/log:/docker/log --link docker_mysql:mysql --link docker_redis:redis -d docker_php:latest 
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


#### mysql
docker pull mysql:5.7
> docker run -p 33061:3306 --name docker_mysql -v D:/docker_www/log/mysql:/logs -v D:/docker_www/mysql_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7

#### redis 
##### 拉取镜像
>  docker pull redis
##### 构建自定义镜像
>  docker build -t docker_redis .
##### 创建容器
> docker run -p 63791:6379 --name docker_redis -v D:/docker_www/redis_data:/data  -d docker_redis:latest redis-server --appendonly yes --requirepass "123456"

> docker exec -it 43f7a65ec7f8 redis-cli 连接客户端

### php代码连接mysql
> $servername 为php容器的名称或宿主机的IP

```
<?php
$servername = "docker_mysql";
$username = "root";
$password = "123456";
 
 
try {
    $conn = new PDO("mysql:host=$servername;", $username, $password);
    echo "连接成功"; 
}
catch(PDOException $e)
{
    echo $e->getMessage();
}
```

### php代码连接redis
> $servername 为宿主机的IP

```
$redis = new Redis();
$servername = '192.168.199.196';
$redis->connect($servername, 63791); //连接Redis
$redis->auth('123456'); //密码验证
$redis->select(2);
$redis->set( "testKey" , "Hello Redis");
```