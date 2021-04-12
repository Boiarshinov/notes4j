---
title: "Tomcat"
tags:
  - web
  - external_lib
draft: false
---

**Tomcat** - это веб-сервер, совмещенный с контейнером сервлетов.

Аналогом Tomcat является [Jetty](jetty.md).

Хотя Tomcat и может выполнять функцию веб-сервера, обычно вместо этого используется веб-сервер Apache, а Tomcat используется просто в качестве контейнера [сервлетов](../servlets/servlet.md).

---
## Контейнер сервлетов
Контейнер сервлетов Tomcat написан на Java.
Как контейнер сервлетов Tomcat обеспечивает:

- управление соединениями, создание сокетов и пр.
- управление жизненным циклом сервлетов
- поддержка многонитевости - каждый сервлет исполняется в своем потоке
- преобразование JSP в контент

---
## Обработка запросов
При поступлении запроса Tomcat выполняет следующее:

- Создание нити для обработки запроса
- Создание объектов responce и request
- Поиск в дескрипторе развертывания подходящего сервлета, который будет обрабатывать запрос, вызов метода service() сервлета
- Получение объекта response от сервлета
- Смерть нити (либо ее возвращение в пул), удаление объектов response и request

---
## К изучению

- [X] Настройка Tomcat от Алишева: https://www.youtube.com/watch?v=hk4rzwH9-kY&list=PLAma_mKffTOTTFqIkLXgHqVuL6xJhb0mr&index=3
- [X] Описание Tomcat и различие версий на вики: https://ru.wikipedia.org/wiki/Apache_Tomcat
- [X] Запуск tomcat на IDEA CE: https://www.shortn0tes.com/2017/01/intellij-idea-community-edition-tomcat.html
- [X] Изи подключение Tomcat на IDEA UE: https://javarush.ru/groups/posts/305-sozdanie-prosteyshego-web-proekta-v-intellij-idea-enterprise-edition-poshagovo-s-kartinkami