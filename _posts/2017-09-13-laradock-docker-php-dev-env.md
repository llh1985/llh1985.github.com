---
layout: post
title: laradock-基于docker的php开发(dev)环境(env)
categories: Other
description: 基于docker的php开发(dev)环境(env)
keywords: laradock, docker, php, 开发环境
---

2017-09-13-laradock-docker-php-dev-env.md   
    
```
###可能用到的命令
###http://laradock.io
git clone https://github.com/Laradock/laradock.git
cd laradock
cp env-example .env

vim docker-compose.yml
vim nginx/nginx.conf
vim nginx/sites/laravel.conf.example

docker-compose up -d nginx mysql redis beanstalkd
docker-compose exec workspace bash
docker-compose up
docker-compose restart
docker-compose stop
```
  
  
