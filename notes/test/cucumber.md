---
title: "Cucumber"
tags:
  - test
  - external_lib
draft: true
---

# Cucumber

__Cucumber__ - это набор адаптеров между синтаксисом Gherkin и тестовыми фреймворками в различных языках.
В этой заметке рассматривается только интеграция с Java.

__Ghrekin__ - это набор синтаксических правил для написания тест-кейсов, которые достаточно просто поддаются автоматическому парсингу инструментами вроде Cucumber. 
Помимо Cucumber с Gherkin умеют работать и другие тулы, например, JBehave.
```gherkin
Scenario: Transfer money
  Given Artem has 200$ on account
  Given Valera has 10$ on account
  When Artem transfer Valera 100$
  Then Artem has 100$ on account
  Then Valera has 110$ on account
```

Тест-кейсы описываются с помощью синтаксиса Gherkin и сохраняются в файлы с расширением `.feature`.
На верхнем уровне в каждом файле описывается название тестируемой фичи после ключевого слова `Feature`.
Каждый тест-кейс называется _сценарием_ и объявляется ключевым словом `Scenario`.
Каждая строчка сценария называется _шагом_.

Cucumber линкует каждый шаг сценария с каким-либо методом в Java-коде.
Такие методы называются __Step Definition__.


---
## Подключение
Для подключения Cucumber к проекту нужно импортировать библиотеку с Cucumber API и адаптер между Cucumber и вашим тестовым фреймворком.
```kotlin
testImplementation("io.cucumber:cucumber-java:7.15.0")
testImplementation("io.cucumber:cucumber-junit-platform-engine:7.15.0")
```

---
## К изучению
- [ ] [Официальная документация](https://cucumber.io/docs/guides/overview/) (Требует VPN)