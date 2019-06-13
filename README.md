# docker_php_env

#### nginx
```
docker run -d  -p 8083:80 --name nginxtest -v ~/docker_learn/nginxtest/www:/docker/www -v ~/docker_learn/nginxtest/log:/docker/log -v ~/docker_learn/docker_file/nginx/conf.d:/etc/nginx/conf.d:ro --link phptest:php nginx:filetest
```

#### php
```
docker run --name  phptest -v ~/docker_learn/nginxtest/www:/docker/www ~/docker_learn/nginxtest/log:/docker/log -v  -d php:filetest
```