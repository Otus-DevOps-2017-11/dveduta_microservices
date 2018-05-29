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
 * Задание со звездочкой:
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
 
 ## Домашняя работа 16
 
 * Переместили предыдущие работы в папку docker-monolith
 * Распаковали архив с reddit-microservices
 * Сделали Dockerfile-ы для микросервисов post-py, comment и ui
  * Улучшения: перемещение команд для более активного использования кеша, объединение команд в одну через &&, удаление дублирования команд (add два раза), применение советов линтера по зачистке деятельности apt-get
 * Собрали образы из докерфайлов
  * ui после comment собрался быстрее ибо кеш (причем не только на from в данном случае)
 * Запустили приложения (сделали bridge, запустили контейнеры из образов в бридже, добавили сетевые алиасы)
 * Проверили работу приложения (работает)
 * Остановили контейнеры
 * Поменяли/улучшили докерфайл для ui 
  * Сборка началась с шага 'RUN apt-get update -qq...' (9, в данном случае)
  
```
reddit-microservices (docker-3)]$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
denveduta/ui            2.0                 3ec681de6138        4 minutes ago       393MB
denveduta/ui            1.0                 f4125122301d        45 minutes ago      768MB
```

* Перезапустили приложение с обновленным образом ui, проверили - поста нет
* Создали docker volume для хранения данных между перезапусками
* Перезапустили с подключением тома, добавили пост
* Перезапустили еще раз - пост на месте
* Оптимизировали сборку контейнеров

## Домашняя работа 17

* Запустили joffotron/docker-net-tools с `--network none` - доступен только localhost
* Запустили joffotron/docker-net-tools с `--network host` - доступные интерфейсы контейнера полностью совпадают с хостом, где контейнер запущен
* Запустили 4 раза `docker run --network host -d nginx` - ошибок при последовательном запуске выдано не было, однако работает только первый контейнер. Остальные контейнеры не смогли запуститься из-за конфликта за занятие порта 80
```
[dveduta_microservices (docker-4)]$ docker logs  43fe2e53b026
...
2018/03/31 10:36:27 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address already in use)
```
* Остановили и зачистили контейнеры
---
* задание со звездочкой пропущено
---
* Создали bridge-сеть в docker
```
docker network create reddit --driver bridge
```
* Запустили наш проект reddit с использованием bridge-сети
```
docker run -d --network=reddit mongo:latest
docker run -d --network=reddit denveduta/post:1.0
docker run -d --network=reddit denveduta/comment:1.0 
docker run -d --network=reddit -p 9292:9292 denveduta/ui:1.0
```
* Контейнеры не "видят" друг друга, их имена не прописаны
* Пересоздаем с прописанными именами
```
docker kill $(docker ps -q)
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post denveduta/post:1.0
docker run -d --network=reddit --network-alias=comment denveduta/comment:1.0 
docker run -d --network=reddit -p 9292:9292 denveduta/ui:1.0
```
* Проект жив и работает.
* Пересоздаем контейнеры так, чтобы ui не имел доступа к базе.
* Останавливаем старые контейнеры, создаем раздельные сети
```
docker kill $(docker ps -q)
docker network create back_net --subnet=10.0.2.0/24
docker network create front_net --subnet=10.0.1.0/24
```
* Запускаем контейнеры в соответствующих сетях
```
docker run -d --network=front_net -p 9292:9292 --name ui  denveduta/ui:1.0
docker run -d --network=back_net --name comment  denveduta/comment:1.0
docker run -d --network=back_net --name post  denveduta/post:1.0
docker run -d --network=back_net --name mongo_db --network-alias=post_db --network-alias=comment_db mongo:latest
```
* Провал - при старте можно подключить только одну сеть. До-подключаем `post` и `comment` в сеть `front_net`
```
docker network connect front_net post
docker network connect front_net comment
```
* Успех
---
* задание со звездочкой пропущено
---
* Docker-compose установлен
* Используя предоставленный docker-compose.yml запустили проект

```
export USERNAME=denveduta
docker-compose up -d
docker-compose ps
            Name                          Command             State           Ports
--------------------------------------------------------------------------------------------
redditmicroservices_comment_1   puma                          Up
redditmicroservices_post_1      python3 post_app.py           Up
redditmicroservices_post_db_1   docker-entrypoint.sh mongod   Up      27017/tcp
redditmicroservices_ui_1        puma                          Up      0.0.0.0:9292->9292/tcp
```
* проверили - проект жив и работает.

### Самостоятельное задание

* Изменили docker-compose под вариант с несколькими сетями 
```
networks:
  back_net:
    driver: bridge
    ipam:
     config:
       - subnet: 10.0.2.0/24
  front_net:
    driver: bridge
    ipam:
     config:
       - subnet: 10.0.1.0/24
```
* Параметризовали порт публикации UI (и проверили создав правило файерволла для этого порта), версии сервисов.
* Вынесли параметры в дефолтный файл переменных окружения .env (иначе пришлось бы указывать его каждый раз вручную)
* Сделали .env.example , вынесли .env в исключения
* Проверили что переменные подгружаются из файла автоматически