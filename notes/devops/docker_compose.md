---
title: "Docker Compose"
tags:
  - devops
draft: false
---

# Docker Compose

Docker Compose - это утилита, которая позволяет описать требуемые [docker](docker.md)-контейнеры и взаимосвязи между ними, а затем запустить их в требуемом порядке.
Описание контейнеров и их взаимосвязей делается с помощью декларативной записи в текстовом файле `docker-compose.yaml`.
Запуск контейнеров с учетом описанными взаимосвязями производится с помощью консольной утилиты `docker-compose cli`.

Чаще всего docker-compose используется при локальной разработке приложений, либо для конфигурации тестовых стендов.
Для боевых стендов docker-compose не подходит, т.к. он деплоит все сервисы на одной машине, а на проде как правило для надежности сервисы разворачиваются на разных серверах.


## `docker-compose.yaml`
`docker-compose.yaml` имеет жесткую структуру, которая задается с помощью json-схемы.
Можно найти несколько версий json-схем, т.к. требования к структуре менялись с развитием инструмента.
- **версия 1** - устарела, не рекомендуется к использованию
- **версия 2** - считалось, что идеальна для локальной разработки
- **версия 3** - считалось, что нужна в первую очередь для использования в Swarm и Kubernetes
- **композитная версия** - слияние версий 2 и 3, позволяющее использовать фичи обеих. Является наиболее актуальной

Название файла `docker-compose.yaml` является дефолтным, его можно назвать и по-другому, но тогда при использовании docker-compose cli придется указывать имя файла с декларациями.

```yaml
version: '2' # Не указывается, если используется композитная версия
services:
  servicename:
    image: image/name # Название образа
    depends_on: # Зависимость между контейнерами. Текущий контейнер будет запущен только после того, как поднимутся все, от кого он зависит
      - service2name
    environment: # Переменные окружения
      key: value
    volumes: # Задаем персистентные хранилища
      - .:/dir # Здесь точка является алиасом для $(pwd)
    ports: # Проброс портов
      - "8080:80"
    # И много других возможных параметров

volumes: # Аналогично docker volume create

# Можно не прописывать, если достаточно того, чтобы все контейнеры были в одной сети. Bridge будет создан по умолчанию
networks: # Аналогично docker network create
```

**Важно!** Если сервисы должны общаться друг с другом, то вместо `localhost` нужно писать название сервиса.
Пример: допустим в .yaml файле мы поднимаем kafka и zookeeper. Kafka должна знать входную точку в zookeeper. Поэтому в переменные окружения kafka мы запишем: `KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181`.


### Сборка образа
При разработке приложений хочется иметь возможность пощупать их локально.
Но тогда при каждом изменении придется сначала пересобирать образ, публиковать его в реестре и только потом использовать в docker compose.
Чтобы облегчить эту задачу, можно собирать образ сразу с помощью docker compose.
Для этого нужно в разделе `build` сослаться на докерфайл и задать необходимые настройки:
```yaml
version: '2'
services:
  building_service:
    build: 
      context: .
      dockerfile: Dockerfile
```


## Docker Compose CLI
Консольная утилита идет в комплекте с docker на Windows и Mac. На Linux ее нужно скачать отдельно.
С версии docker 20.10.13 функционал `docker-compose` был полностью включен в основной `docker cli`, поэтому теперь дефис ставить необязательно, все команды можно начинать с `docker compose` через пробел.

Часто употребляемые команды:
- `docker-compose up` - настраивает volumes и networks, создает и поднимает все контейнеры. Часто используется с ключом `-d`, чтобы отпустить консольку
- `docker-compose down` - останавливает все контейнеры и удаляет их, их volumes и networks
- `docker-compose stop` - останавливает контейнеры, но не удаляет их
- `docker-compose start` - стартует контейнеры
- ... много других

По умолчанию `docker compose` будет использовать конфигурацию из файла `docker-compose.yaml`, который лежит в открытой директории. 
Если имя файла отличается от стандартного, то нужно передать его с помощью ключа `-f custom-docker-compose.yaml`.


## Примеры

Пример реального `docker-compose.yaml`:
```yaml
version: '2'
services:
  # service discovery for kafka
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
    ports:
      - "22181:2181"

  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - "29092:29092"
    environment:
      KAFKA_BROKER_ID: 1 # Node id inside broker cluster. Should be unique for cluster
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181 # link to ZooKeeper nodes
      # PLAINTEXT means that there is no security protocols used
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092,PLAINTEXT_HOST://localhost:29092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1

  # opensource UI tool for Kafka
  kafdrop:
    image: obsidiandynamics/kafdrop:latest
    depends_on:
      - kafka
    ports:
      - "9123:9000"
    environment:
      KAFKA_BROKERCONNECT: kafka:9092
      JVM_OPTS: -Xms32M -Xmx64M
```


---
## К изучению

- [ ] [Официальная документация](https://docs.docker.com/compose/)
- [ ] [Правила заполнения `docker-compose.yml`](https://docs.docker.com/compose/compose-file/)
- [X] [Документация на docker-compose cli](https://docs.docker.com/compose/reference/)
- [X] [Курс Docker Mastery](https://www.udemy.com/course/docker-mastery/) на Udemy в части касающейся
