---
title: "JaCoCo"
tags:
  - test
draft: false
---

# JaCoCo

**JaCoCo** - Java Code Coverage - инструмент для измерения [тестового покрытия](../test/test_coverage.md) программ, написанных на JVM-based языках.

Данные о тестовом покрытии собираются в рантайме при прогонке тестов на уровне инструментации байткода с помощью *Java Agent*.
Благодаря этому JaCoCo может работать не только с Java, но и с другими JVM-based языками.

JaCoCo позволяет измерять тестовое покрытие по одному из следующих критериев:
- строки кода
- ветви кода
- инструкции
- методы
- цикломатическая сложность

JaCoCo может генерировать отчет в форматах:
- HTML - для изучения разработчиком / тестировщиком
- XML - для последующего машинного анализа
- CSV - для последующего машинного анализа


## Использование

Базовый способ использования JaCoCo - это Java Agent.
Помимо этого существует CLI утилита, которая может анализировать код оффлайн.
Но чаще всего JaCoCo используют в виде плагинов для популярных сборщиков: Maven и Gradle.
Далее рассмотрен Gradle Plugin.

Для подключения в плагины проекта нужно добавить:
```kotlindsl
plugins {
    jacoco
}
```

Настроить плагин можно следующим образом:
```kotlindsl
jacoco {
    toolVersion = "0.8.7"
    reportsDirectory.set("$buildDir/reports/jacoco")
}
```

Плагин предоставляет две таски:
- `jacocoTestReport` - генерация отчета по информации, собранной Java Agent
- `jacocoTestCoverageVerification` - проверка соответствия тестового покрытия quality gate'ам, заданным в проекте. При несоответствии - выполнение таски завершается неуспешно


### Генерация отчетов

В таске `jacocoTestReport` можно задать желаемый формат отчета и директорию куда он будет помещен.
Таска `jacocoTestReport` обычно настраивается таким образом, чтобы выполняться сразу после прогона тестов.
```kotlindsl
tasks.jacocoTestReport {
    reports {
        html.required.set(true)
        xml.required.set(false)
        csv.required.set(false)
    }
    dependsOn(tasks.test)
}

tasks.test {
    finalizedBy(tasks.jacocoTestReport)
}
```


### Проверка Quality Gate'ов

С помощью таски `jacocoTestCoverageVerification` можно настроить определенные правила, при которых билд будет падать, если тестовое покрытие не удовлетворяет принятым стандартам. 
Правил может быть несколько.
Вызов проверки можно настроить одновременно с другими проверками (например checkstyle), либо вызывать в пайплайне отдельно.
```kotlindsl
tasks.jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                counter = "LINE" //Критерий измерения тестового покрытия
                minimum = "0.40".toBigDecimal() //Доля. От 0 до 1
            }
        }
    }
}

tasks.check { //таска Java-плагина
    finalizedBy(tasks.jacocoTestCoverageVerification)
}
``` 

На выбор доступны следующие критерии измерения тестового покрытия:
- `LINE` - строки кода, по которым проходил поток исполнения
- `BRANCH` - ветви исполнения (блоки в `if`, `switch`), по которым проходил поток исполнения
- `INSTRUCTION` - строки байткода, по которым проходил поток исполнения
- `CLASS` - классы, в которые хотя бы раз заходил поток исполнения
- `COMPLEXITY` - цикломатическая сложность
- `METHOD` - методы, в которые хотя бы раз заходил поток исполнения

На мой взгляд, лучше всего отражает полноту тестового покрытия метрика `BRANCH`


---
## К изучению

- [X] [Официальная документация](https://www.jacoco.org/jacoco/trunk/doc/); 
- [X] Документация на [Jacoco Gradle Plugin](https://docs.gradle.org/current/userguide/jacoco_plugin.html)