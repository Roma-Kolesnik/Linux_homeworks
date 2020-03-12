## What is docker-compose ?

Так как мы выяснили что для запуска полноценного приложения нам требуется иметь несколько разных контейнеров которые могут быть по своему настроены.
Процесс сборки и запуска такого приложения становиться достаточно ресурсоемкой задачей и доставляет большие трудности.

Для облегчения этого процесса была создана утилита под названием docker-compose.

Docker-compose утилита разработаная для автоматизации процесса сборки и развертывания N количества контейнеров.
Так же его еще называют аркестратором ибо он по сути руководит нашим ансамблем из разных контейнеров.

У вас есть один файл в котором описана конфигурация вашего приложения которое состоит из микросервисов.

Таким образом у нас есть единый интерфейс для взаимодействия с нашим зоопарком микросервисов.

## Review docker-compose.yml

```yml

version: '3'

services:
  web:
    build: app/
    ports:
    - "11000:8000"
    volumes:
      - "./app:/allstars"
    depends_on:
      - "rabbit1"

  ws:
    build: ws_logs/
    ports:
      - "8007:8007"
    volumes:
      - "./ws_logs/logs:/home/yozh/LOGS"

  rabbit1:
    image: "rabbitmq:3-management"
    hostname: rabbit1
    environment:
      RABBITMQ_ERLANG_COOKIE: "SWQOKODSQALRPCLNMEQG"
      RABBITMQ_DEFAULT_USER: "rabbitmq"
      RABBITMQ_DEFAULT_PASS: "rabbitmq"
      RABBITMQ_DEFAULT_VHOST: "/"

    ports:
    - "15672:15672"
    - "5672:5672"

  logs:
    build: ./logs
    volumes:
     - "./logs:/logs"
    depends_on:
      - "log_db"
    ports:
      - "12000:8008"

  logdb:
    image: "postgres"
    environment:
      POSTGRES_USER: "dev"
      POSTGRES_PASSWORD: "root"
      POSTGRES_DB: "logs"
    volumes:
      - "./pg_data:/var/lib/postgresql/data"

```

`Services` - специальная дериктива после которой docker-compose будет искать название сервисов с которыми ему придется работать.


`web` - Название вашего сервиса(по этому имени можно обратиться к контейнеру по сети, при условии что вы находитесь в одной сети.)


`build` - путь к докер файлу контейнера который вы будете использовать для данного сервиса.


`ports` - порт форвадинг или проброс портов, аналогично `docker run -p 5000:5000`


`volumes` - шаринг дериктории, аналогично `docker run -v /path/to/shared:/docker/path/to/shared`


`depends_on` - зависимости от других сервисов, не запускать этот пока не будет готов тот который прописан


`hostname` - установка имени по которому можно обращаться по сети, по умолчанию используется имя сервиса.


`environment` - переменные окружения, аналогично $USER, $HOME


`image` - указание образа с докер хаба.

## How to install

[INSTALL MANUAL](https://docs.docker.com/compose/install/)

## Commands
`docker-compose up` - попытаеться найти docker-compose.yml в той папке в которой вы сейчас находитесь и запустить ваши сервисы.


`docker-compose down` - Делает в точности наоброт что делает команда выше, то есть выключает ваши контейнеры.


`docker-compose build` - Произведет сборку контейнеров и после попытается их запустить.


`docker-compose logs service_name` - просмотр STDOUT контейнера.


`docker-compose exec service_name /bin/bash` - зайти в контейнер в оболочку bash.



Так же можно проивзодить сборку и запуск отдельных сервисов как представлено выше.

Весь набор команд можно найти на https://docs.docker.com/compose/reference/overview/

или набрав соотвествующую команду `docker-compose --help`


## Practice!


