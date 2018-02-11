# dveduta_microservices
## Домашняя работа 14
 * Установили docker, docker-compose, docker-machine
 * Запустили hello-world - всё ок
 * Посмотрели состояние образов и контейнеров
 ```
 [alfar@alfarPC dveduta_microservices (master)]$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
[alfar@alfarPC dveduta_microservices (master)]$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                          PORTS               NAMES
28d0607b958b        hello-world         "/hello"            About a minute ago   Exited (0) About a minute ago                       friendly_thompson
[alfar@alfarPC dveduta_microservices (master)]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              f2a91732366c        2 months ago        1.85kB
```
 * Контейнеры ubuntu:16.04, в первом из которых `echo 'Hello world!' > /tmp/file`
 * Вывод списка контейнеров с абсолютной датой создания вместо относительной
 ```
 docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.CreatedAt}}\t{{.Names}}" 
 ```
 * Нашли, запустили и зашли в контейнер с `/tmp/file`
 * Последовательность для выхода из контейнера `Ctrl + p, Ctrl + q`
 * `docker run -it ubuntu:16.04 bash`
 * `docker run -dt nginx:latest`
 * Зашли в контейнер с `/tmp/file` с помощью `docker exec -it 7b908860fe3a bash`
 * Закомитили контейнер в образ `docker commit 7b908860fe3a dveduta/ubuntu-tmp-file`
 * Записали вывод списка образов в файл `docker images > docker-1.log`
 * Задание со звездочкой (но под вопросом, не совсем понятно что именно требуется)
 * Выключение запущенных контейнеров
 ```
$ docker ps -q
$ docker ps -q | xargs docker kill
```
 * Занятое пространство `docker system df`
 * Удаление контейнеров `docker rm $(docker ps -a -q)`
 * Удаление образов `docker rmi $(docker images -q)`