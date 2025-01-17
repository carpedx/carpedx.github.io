---
layout: fragment
title: MQ安装延迟队列插件
tags: [mq]
description: MQ安装延迟队列插件
keywords: mq
---



**Docker中RabbitMQ安装延迟队列插件**

------



```
docker ps

docker exec -it b195 /bin/bash

cd /opt/rabbitmq/sbin

rabbitmqctl status

cd /opt/rabbitmq/plugins

wget https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases/download/v3.12.0/rabbitmq_delayed_message_exchange-3.12.0.ez

rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```



> ps：如果安装失败，请查看RabbitMQ插件版本是否匹配



安装成功，在RabbitMQ Type中会多出一个：x-delayed-message

<img src="/images/fragments/php/docker-rabbitmq-x-delayed-message_step1.webp" />
