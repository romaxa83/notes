#queue #rabbitmq 

#### 🔹 Articles

- https://kt.team/hr/blog/rabbitmq
- SYMFONY + RABBITMQ - https://habr.com/ru/post/338950/?_ga=2.65865510.986163674.1555313967-799489654.1535462128
- RABBITMQ + Nodejs- https://habr.com/ru/post/447074/?fbclid=IwAR3aaatRZ0VQcS4gPsaq6QJcDpWnnuRLvRe_W022l2kt5jCVk4bQBu92fRI

- http://www.phphighload.com/2013/08/amqp-rabbitmq.html
- https://thewebland.net/development/devops/rabbitmq/

- КНИГА ПО RabbitMq - http://onreader.mdl.ru/RabbitMQInDepth/content/index.html
- ПРИМЕРЫ ИСПОЛЬЗОВАНИЯ - https://github.com/rabbitmq/rabbitmq-tutorials/tree/master/php

#### 🔹 Install

```bash
# Ubuntu

# install erlang
wget https://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
sudo dpkg -i erlang-solutions_1.0_all.deb
sudo apt-get update
sudo apt-get install erlang erlang-nox -y

# install rabbitmq server
echo 'deb http://www.rabbitmq.com/debian/ testing main' | sudo tee /etc/apt/sources.list.d/rabbitmq.list
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
sudo apt-get update
sudo apt-get install rabbitmq-server -y
```
Установка на Ubuntu_18.04
https://tecadmin.net/install-rabbitmq-server-on-ubuntu/



2) -- install rabbitmq server

=========================================================================================
библиотека для работы с amqp


=========================================================================================