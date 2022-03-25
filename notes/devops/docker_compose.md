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


## `docker-compose.yaml`
`docker-compose.yaml` имеет жесткую структуру, которая задается с помощью json-схемы.
Можно найти несколько версий json-схем, т.к. требования к структуре менялись с развитием инструмента.

Название файла `docker-compose.yaml` является дефолтным, его можно назвать и по-другому, но тогда при использовании docker-compose cli придется указывать имя файла с декларациями.

```yaml
version: '2'
services:
  servicename:
    image: image/name # Название образа
    depends_on: # Зависимость между контейнерами. Текущий контейнер будет запущен только после того, как поднимутся все, от кого он зависит
      - service2name
    enviroment: # Переменные окружения
      key: value
    volumes: # Задаем персистентные хранилища
      - .:/dir # Здесь точка является алиасом для $(pwd)
    ports: # Проброс портов
      - "8080:80"
    # И много других возможных параметров

volumes: # Аналогично docker volume create

networks: # Аналогично docker network create
```


## Docker Compose CLI
Консольная утилита идет в комплекте с docker на Windows и Mac. На Linux ее нужно скачать отдельно.

Часто употребляемые команды:
- `docker-compose up` - настраивает volumes и networks и поднимает все контейнеры
- `docker-compose down` - останавливает все контейнеры и удаляет volumes и networks


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

- [X] [Курс Docker Mastery](https://www.udemy.com/course/docker-mastery/) на Udemy в части касающейся
