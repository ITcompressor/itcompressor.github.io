docker, docker-compose

**tags** `isolation` `docker-compose`
**time** 2021-11-28 16:23

## Манипуляции с контейнерами

### Просмотр всех контейнеров

```bash
docker ps -a
```

### Просмотр всех запущенных контейнеров

```bash
docker ps
```

### Удаление контейнера

```bash
docker rm $container_name
```

### Удаление всех неактивных контейнеров

```bash
docker rm -v $(docker ps -aq -f status=exited)
```

### Запуск контейнера (если контейнер стопнут)

```bash
docker start $container_name
```

### Просмотр информации о контейнере

```bash
docker inspect $conainer_name
```

### Просмотр лога контейнера

```bash
docker log $container_name
```

### Запуск контейнера с пробросом портов (80 - контейнер, 8080 - хост)

```bash
docker run -d -p 8080:80 $container
```

### Запуск bash контейнера

```bash
docker exec -it $container_name bash
```

### Пример запуск утилиты контейнера

```bash
docker exec infra_mongodb_1 mongoexport -d devbot -c botLog -o /root/test.json
```

## Работа с имейджами

### Создание имейджа

```bash
docker run -it --name myapp --hostname myapp ubuntu bash
apt update
apt install cowsay
docker commit myapp MY_DOCKER_ID/MY_IMAGE
docker push MY_DOCKER_ID/MY_IMAGE
```

### Билд dockerfile

```bash
docker build .
```

## Основные команды docker-compose

### Запуск в бекграунде по файлу

`-d` - бекграунд
`-f` - файл

```bash
docker-compose -f mongo.yaml up -d
```

Сам mongo.yaml может выглядеть так

```yaml
version: '3.3'

services:

  mongodb:
    image: mongo:3.6.21 # for legacy mongeez
    restart: always
    environment:
      - MONGO_DATA_DIR=/data/db
    ports:
      - 127.0.0.1:27017:27017 # if need only localhost
    command: mongod
    expose:
      - '27017'
    volumes: # config for vds
      - "/root/mongo:/data/db" # data
      - "~/:/root" # home
```

### Стоп

```bash
docker-compose stop
```
