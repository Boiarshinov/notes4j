---
title: "Allure"
tags:
  - tool
  - testing
draft: True
---

# Allure

Allure - это утилита, позволяющая визуализировать результат прогонки тестов.

Allure сам не прогоняет тесты. 
Он встраивается в процесс прогонки тестов различных тестовых фреймворков, как например [JUnit](../test/junit.md), TestNG, Cucumber, и других.
При интеграции с JUnit, к примеру, Allure встраивается в жизненный цикл тестового фреймворка с помощью [AOP](../java/aop.md).
Результаты анализа помещаются в директорию `allure-results` и представляют собой `.json` файлы с метаинформацией о прогонке тестов.
Затем можно на основе данных файлов сгенерировать web-страничку с отчетом.
Для этого поднимается небольшой jetty-сервер, а отчет открывается в обычном браузере.

Особенно полезной фичей Allure является визуализация исполнения тесткейсов во времени.
Можно посмотреть во сколько потоков гонялись тесты, какие тест-кейсы выполнял каждый из потоков, равномерно ли были распределены тест-кейсы между потоками.

В первоначальном виде Allure - это CLI утилита.
Но для нее есть gradle и maven плагины, которые позволяют делать все (???) то же самое.

## Подключение
Проще всего подключить allure к своему Java проекту с помощью gradle plugin:
```kotlin
plugins {
    java
    id("io.qameta.allure") version "2.11.2"
}

allure {
    adapter {
        frameworks {
            junit5 {//used by default
                // configuration if needed
            }
        }
    }
}
```
Плагин автоматически подтянет необходимые зависимости под выбранный фреймворк (а может и нет).

Теперь при прогонке тестов в директорию `/build/allure-results` будут помещаться метаданные о выполненных тесткейсах.

Для генерации отчета используется таска `allureReport`.
```shell
./gradlew test allureReport
```


---
## Основные концепции

### Testcase name
Название тесткейса.
При использовании JUnit 5 автоматически будут подтянуты названия методов, над которыми стоит `@Test`. 
Если над методом стоит аннотация `@DisplayName`, то будет название тесткейса будет браться из нее.

При использовании Cucumber в качестве названия тест-кейса будет использовано название сценария, вытащенное из `.feature` файла.

Дополнительно можно описать сценарий подробнее с помощью аннотации `@Description`.
Информация, указанная там, добавится в специальный раздел тесткейса.
```java
@Test
@Description("My lovely test")
void test() {
  //test routine
}
```

### Step
Шаг - это единица исполнения тестового сценария.
Шаги могут переиспользоваться в разных сценариях.
Благодаря шагам, в allure report можно раскрыть ход исполнения тест-кейса, посмотреть на каком шаге упал тест-кейс, изучить приложенные файлы к каждому из тест-кейсов.
Например, шагом может быть HTTP-запрос, тогда к шагу могут быть приложены в виде Attachment сам запрос и ответ на него.
Или шагом может быть обращение к базе данных, тогда к шагу могут быть приложены 
```
Сценарий
  |- HTTP-запрос на создание заказа
  |- Вытаскиваем из базы данных данные по заказу спустя некоторый таймаут
  |- Проверяем, что заказ находится в статусе "В работе"
```

Шаг определяется с помощью аннотации `@Step`
```java
@Step("Create order")
OrderCreatedDto sendRequest(CreateOrderDto createOrderDto) {
  HttpRequest request = mapToRequest(createOrderDto);
  HttpResponse response = httpClient.send(request);
  return mapFromResponse(response);
}
```

В аннотации можно использовать простенький expression language, построенный на [рефлексии](../java/reflection.md).
```java
@Step("Create order with {createOrderDto.totalPrice} price")
OrderCreatedDto sendRequest(CreateOrderDto createOrderDto) {
  //...
}
```

### Attachment
К шагам тесткейса могут быть приложены Attachment'ы.
Attachment'ы могут быть текстовыми или бинарными.

Для текстовых вложений можно поставить аннотацию `@Attachment` над методом, возвращающим строку.
Эта строка будет прикреплена к шагу.
```java
@Attachment
private String attachToReport(String content) {
  return content;
}
```

Либо можно воспользоваться утилитным методом:
```java
Allure.addAttachment("attachment", "attachment content");
```

Для бинарных вложений, нужно чтобы метод возвращал массив байт.
В аннотации можно указать [MIME-тип](../web/mime.md) вложения.
```java
@Attachment(type = "image/png")
private byte[] attachToReport(byte[] content) {
    return content;
}
```

Либо с помощью императивного API:
```java
try (InputStream inputStream = AllureAttachmentTest.class.getResourceAsStream("/allure/attachment.png")) {
    Allure.addAttachment("attachment", inputStream);
}
```


---
## К изучению
- [ ] [Официальная документация](https://docs.qameta.io/allure/)
- [X] [Allure Gradle Plugin](https://github.com/allure-framework/allure-gradle)
