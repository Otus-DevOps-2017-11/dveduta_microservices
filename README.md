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
 
 ## Домашняя работа 15
 * Создали проект в google cloud (docker)
 * Настроили gcloud на этот проект
 * Создали docker-machine
 ```
$ docker-machine create --driver google \
 --google-project savvy-aileron-194911 \
 --google-zone europe-west1-b \
 --google-machine-type g1-small \
 --google-machine-image $(gcloud compute images list --filter ubuntu-1604-lts --uri) \
 docker-host
 ```
 * Проверили что успешно создалась
 ```
$ docker-machine ls
NAME          ACTIVE   DRIVER   STATE     URL                         SWARM   DOCKER        ERRORS
docker-host   -        google   Running   tcp://35.205.132.234:2376           v18.02.0-ce
 ```
 * Активировали в качестве DOCKER_HOST
 ```
 $ docker-machine env docker-host
 export DOCKER_TLS_VERIFY="1"
 export DOCKER_HOST="tcp://35.205.132.234:2376"
 export DOCKER_CERT_PATH="/home/alfar/.docker/machine/machines/docker-host"
 export DOCKER_MACHINE_NAME="docker-host"
 # Run this command to configure your shell:
 # eval $(docker-machine env docker-host)
 $ eval $(docker-machine env docker-host)
 ```
---
 * Задание соз звездочкой:
  * Сравнить вывод
   ```
   docker run --rm -ti tehbilly/htop
   ```
   ```
   docker run --rm --pid host -ti tehbilly/htop
   ```
  Второй запущен с использованием системного адресного пространства. Соответственно root в контейнере = root в системе, и имеет полный доступ к системным процессам. Такой контейнер совершенно небезопасен.
---
 * Создали файлы для собственного контейнера.
 * Собрали образ `docker build -t reddit:latest .`
 ```
 ...
Successfully built 4a98951adabc
Successfully tagged reddit:latest
```
 * Проверили - контейнер есть `docker images -a`
 * Запустили контейнер `docker run --name reddit -d --network=host reddit:latest`
 * Добавили правило для файерволла 
 * Проверили работу приложения
 * Авторизовались и залогинились на dockerhub
 * Перетегировали reddit:latest в denveduta/otus-reddit:1.0
 * Запушили denveduta/otus-reddit:1.0 в dockerhub
 ```
 1.0: digest: sha256:5799febf72b9d92003deca07483c9cdb40058cacf46495784ec85c16de874a94 size: 2403
 ```