---
title: "Kafka Streams"
tags:
  - kafka
draft: true
---

# Kafka Streams

Kafka Streams - это фреймворк, предназначенный для организации [потоковой обработки данных](../architecture/streaming.md), основанной на Kafka в качестве источника событий.

Kafka Streams старается занять нишу между сложными аналитическими стриминговыми фреймворками вроде Apache Spark и обычными Java приложениями, написанными на коленке с Consumer'ами и Producer'ами.
От Kafka Streams не взрывается мозг, при этом библиотека позволяет делать те же крутые стриминговые штуки, что и взрослые фреймворки.
При этом деплой и мониторинг Kafka Streams приложения так же прост, как и любых других Java-приложений.

Для хранения агрегатов Kafka Streams использует embedded базу данных ключ-значение RocksDB.

У Kafka Streams существует два основных API:
- низкоуровневый **Processor API**
- высокоуровневый **StreamBuilder API**



- `KStream` - предствления потока в виде стрима
- `KTable` - представление потока в виде таблицы

---
## К изучению
- [ ] Книга Apache Kafka. Потоковая обработка и анализ данных. Глава 11
- [X] [Анонс фреймворка в 2016 г.](https://www.confluent.io/blog/introducing-kafka-streams-stream-processing-made-simple/)
- [ ] [Видео курс от Confluent](https://www.youtube.com/playlist?list=PLa7VYi0yPIH35IrbJ7Y0U2YLrR9u4QO-s) по Kafka Streams