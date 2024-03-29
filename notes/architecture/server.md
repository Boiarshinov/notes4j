---
title: "Server architecture"
tags:
  - multithreading
  - web
draft: true
---

# Архитектура сервера

Существует два основных подхода к организации работы с потоками при реализации сервера:
- __thread-per-request__ - классическая архитектура - каждый пользовательский запрос обрабатывается одним потоком
- __thread-sharing__ - реактивная архитектура - каждое действие может попасть в случайный поток


## Thread-per-request
В этом случае каждый пользовательский запрос обрабатывается одним потоком.
Количество одновременно обрабатываемых запросов ограничено количеством потоков в пуле.
А чем больше потоков, тем больше памяти кушает приложение.

Преимуществами данного подхода являются:
- Простота кода - отсутствуют различные обертки, загрязняющие бизнес-логику
- Возможность использовать контекст потока при обработке запроса. Например, так работает лог-инструмент [MDC](../external_lib/slf4j.md)

На такой архитектуре в Java-мире устроены [сервлеты](../servlets/servlet.md), а следовательно и [Spring MVC](../spring/spring_mvc.md).

## Thread-sharing
В этой архитектуре количество потоков обычно равно количеству ядер процессора.
Задачи разбиваются на кусочки, которые могут быть обработаны асинхронно, а рабочие потоки постоянно переключают свой контекст между этими кусочками.
Примерно так же работает сам процессор: единицы исполнения попадают в ядро, при этом ОС постоянно подсовывает задачки, принадлежащие разным процессам.
Благодаря этому можно обработать неограниченное количество одновременных запросов.

В такой модели недопустимы блокирующие вызовы.
Поэтому для thread-sharing необходимо иметь неблокирующие клиенты для своих внепроцессных зависимостей: баз данных, http-клиентов и т.д.

На такой архитектуре в Java-мире построен Spring WebFlux.

Из-за того, что выполнение кода постоянно прыгает из одного потока в другой, такие процессы тяжело дебажить.


---
## К изучению
- [X] [Курс по многопоточности в Java](https://fillthegaps.getcourse.ru/mt7)