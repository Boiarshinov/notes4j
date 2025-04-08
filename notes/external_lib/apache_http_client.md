---
title: "Apache Http Client"
tags:
  - web
  - external_lib
draft: false
---

# Apache Http Client

Для более удобного отправления запросов с использованием протокола [HTTP](../network/http.md) на удаленные сервера существует множество библиотек. 
Одна из которых: Apache Http Client.

Принцип работы клиента состоит в том, что пользователь формирует запрос, передает его в Http Client и получает ответ, либо исключение при возникновении какой-либо ошибки.

---
## Создание запроса

Для каждого http метода существует свой класс для создания запроса:

- HttpGet
- HttpPost
- HttpPut
- HttpDelete
- и пр.

Объект любого из перечисленных классов можно создать с помощью различных конструкторов (на примере HttpGet):

- `new HttpGet()` - пустой конструктор, все необходимые данные для запроса нужно будет передать позже с помощью сеттеров
- `new HttpGet(String uri)` - передается URI в строковом представлении, по которому будет отправлен запрос.
- `new HttpGet(URI uri)` - передается URI, по которому будет отправлен запрос. Желательно пользоваться именно этим конструктором, потому что он уменьшает возможность совершения ошибки.

### Класс HttpGet

GET запрос можно <mark>todo</mark>

---
## Выполнение запросов

Выполнение запросов совершается с помощью специального объекта, реализующего интерфейс HttpClient. Для создания таких объектов используется класс-фабрика `HttpClients` с методами:

- `CloseableHttpClient createDefault()` - возвращает стандартный HTTP клиент.
- `CloseableHttpClient createSystem()` - возвращает HTTP клиент с системными настройками.
- `CloseableHttpClient createMinimal()` - возвращает HTTP клиент с самой простейшей поддержкой протокола.
- `HttpClientBuilder custom()` - возвращает строитель, с помощью которого можно создать свой собственный супер-навороченный HTTP клиент.

Единственное, что можно сделать с HTTP клиентом - вызвать метод `execute()`, который имеет множество перегрузок.

- `HttpResponse execute(HttpUriRequest)` - выполняет запрос и возвращает результат.
```java
HttpClient httpclient = HttpClients.createDefault();
HttpGet httpget = new HttpGet("http://localhost/");
HttpResponse response = httpclient.execute(httpget);
```

<mark>//Написать про то как можно создавать кастомные HTTP клиенты</mark>

---
## Обработка ответа

Ответ, возвращаемый сервером представлен интерфейсом `HttpResponse`, в котором определены следующие методы для извлечения информации:

- `HttpEntity getEntity()`
- `StatusLine getStatusLine()` - возвращает статус ответа. Содержит в себе версию протокола, код ответа и его текстовое описание. Например: `HTTP/1.1 200 OK`.
- `Locale getLocale()` - возвращает [локаль](../java/l10n_i18n.md).
- Все что касается заголовков:
    - `Header[] getAllHeaders()` - возвращает все заголовки ответа.
    - `Header[] getHeaders(String)` - возвращает все заголовки c заданным именем.
    - `Header getFirstHeader(String)` - возвращает первый заголовок c заданным именем.
    - `Header getLastHeader(String)` - возвращает последний заголовок c заданным именем.
    - `HeaderIterator headerIterator()` - возвращает итератор по всем заголовкам.
    - `HeaderIterator headerIterator(String)` - возвращает итератор по заголовкам с указанным именем.

На мой взгляд не хватает метода, возвращающего стрим заголовков.
Также в нем существуют и сеттеры, но зачем они нужны мне непонятно.

---
## Создание URL - URIBuilder

Для удобного создания идентификаторов ресурсов URL существует специальный класс - URIBuilder.

С помощью данного класса можно по кусочкам собрать URL
```java
URI uri = new URIBuilder()
    .setScheme("http")
    .setHost("www.google.com")
    .setPath("/search")
    .setParameter("q", "httpclient")
    .build();
```


---
## К изучению:
- [ ] Официальная документация: https://hc.apache.org/httpcomponents-client-ga/tutorial/html/fundamentals.html
