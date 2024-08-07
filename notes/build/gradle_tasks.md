---
title: "Gradle Tasks"
tags:
  - build
draft: false
---

# Gradle tasks

Таска - это какая-то работа, например компиляция каких-либо файлов, генерация документации или создание jar.
Таски добавляются в проект при подключении плагинов.
Также таски можно писать самостоятельно.

Посмотреть полный список тасок можно с помощью таски `tasks`:
```shell
./gradlew tasks --all
```

Существует два вида тасок:
- `lifecycle task` - таски, которые сами по себе ничего не делают, но к ним можно подключать другие таски, чтобы сформировать необходимый процесс билда. К таким таскам относится, к примеру, `build`, `clean`, `check`.
- `actionable task` - таски, которые что-то делают. Они могут быть подключены к lifecycle tasks или вызываться сами по себе

Таски можно воспринимать как методы.
У них есть:
- входные данные - файлы, директории
- выполняемые действия
- выходные данные - файлы, директории


---
## Запуск тасок
Таски запускаются с помощью Gradle CLI, либо c помощью Gradle Wrapper скрипта.
```shell
echo "execute task with name 'tasks'"
./gradlew tasks
```

Можно запускать последовательно несколько тасок:
```shell
./gradlew clean composeDownForced composeUp
```

Можно исключать некоторые зависимые таски из исполнения с помощью ключа `-x`
```shell
./gradlew build -x test
```

Если мы запускаем какую-либо таску, которая зависит от большого количества других тасок, то при запуске с ключом `--parallel`, Gradle постарается независимые таски исполнить параллельно:
```shell
./gradlew build --parallel
```


---
## Создание тасок
Создать собственную таску очень просто. 
В корне `build.gradle` достаточно написать:
```kotlin
tasks.register("taskName") { //naming convention: use camelCase

}
```

У каждой таски есть следующие параметры:
- `name` - название таски. Задается при создании
- `description` - описание таски
- `group` - группа, в которую входит таска. Каждая таска может входить только в одну группу

Полезные действия объявляются в таске с помощью методов, принимающих лямбду:
- `doFirst`
- `doLast`

В качестве входных данных одной таски могут использоваться выходные данные другой таски:
```kotlin
tasks.register<Copy>("copyJar") {
    from(tasks.jar) //используются выходные данные таски `jar`
    //...
}
```
В этом случае таска станет автоматически зависимой от тех тасок, чьи выходные данные были использованы.


### Существующие таски
В Gradle существует ряд преконфигурированных тасок.
Например:
- `Zip` - архивирует файлики
- `Copy` - копирует файлики
- `Sync` - то же, что и `Copy`, но перед копированием в указанную директорию, сначала удаляет в ней все файлы
- `Exec` - исполняет скрипт
- много других из пакета `org.gradle.api.tasks`

При создании своей таски, можно унаследоваться от одной из стандартных:
```kotlin
tasks.register<Zip>("zipLiquiScripts") {
    from("$buildDir/resources/db/liquibase")
    destinationDirectory.set("$buildDir/")
    archiveFileName.set("dbScripts.zip")
}
```
Здесь `from` устанавливает директорию, которая будет являться input'ом для таски.

---
## Зависимости между тасками
Таски могут иметь зависимости друг от друга.
Например, запускать тестирование не имеет смысла, если код не скомпилирован, поэтому таска `test` имеет зависимость от таски `testClasses`, а та в свою очередь от таски `classes`.
Таким образом таски выстраиваются в направленный ациклический граф, где таска - это узел, а стрелка - это связь между тасками.

![gradle_default_tasks_dependencies](https://miro.medium.com/max/3336/1*E5JMRbW525OHTa1Op7dGGA.png)

Зависимости между тасками выражаются с помощью различных ключевых слов:
- `dependsOn` - для выполнения таски требуется предварительное выполнение другой таски
```kotlin
task1 { //перед исполнением первой таски всегда будет выполнена вторая таска
    dependsOn(task2)
}
```

- `mustRunAfter` - жестко устанавливает порядок выполнения двух тасок. При этом таски могут вызываться независимо друг от друга.
```groovy
task2.mustRunAfter task1 //при вызове двух тасок вторая всегда будет выполняться после первой
```

- `shouldRunAfter` - указывает Gradle, что таски желательно выполнять в указанном порядке.
```groovy
task2.shouldRunAfter task1 //при вызове двух тасок вторая чаще будет выполняться после первой
```

- `finalizedBy` - после выполнения данной таски должна быть выполнена другая таска
```kotlin
task1 { //при вызове первой таски всегда будет выполнена вторая таска
    finalizedBy(task2)
} 
```


---
## Отключение тасок
Самый простой способ не выполнять таску - выключить ее с помощью параметра `enabled`
```groovy
task1.enabled = false
```

Вызов таски может производиться только при удовлетворении какого-либо условия. Записывается это с помощью метода таски `onlyIf` в который необходимо передать предикат

```groovy
task1.onlyIf { !project.hasProperty('skipTask1') }
```


---
## Параметры тасок
В таски можно добавлять параметры, которые будут доступны во всех местах, где эта таска вызывается. Делается это с помощью `ext`.
```groovy
task taskName {
    ext.taskParam = "value"
}

task print {
    doLast {
        println taskName.taskParam
    }
}
```


---
## Результаты выполнения тасок
При запуске тасок в консоль выводятся результаты их выполнения:

- Нет примечания или `EXECUTED` - таска выполнена
- `UP-TO-DATE` - таска не выполнялась, потому что входные данные не изменились, а выходные уже были получены ранее
- `FROM-CACHE` - результаты выполнения таски были получены из кэша
- `SKIPPED` - таска не выполнялась, т.к. была намерено пропущена с помощью параметров запуска билда.
- `NO-SOURCE` - таска не выполнялась, потому что в ней отсутствовал код для исполнения


---
## Best practices
В мультимодульных проектах рекомендуется создавать отдельные таски, которые будут запускать нужную таску в каждом из сабпроектов.
```kotlin
tasks.register("testAll") {
    dependsOn(subproject.map { ":${it.name}:test" })
}
```

Вместо вызова таски с выключенной другой таской
```shell
./gradlew build -x test
```
лучше создать отдельную таску для этого
```kotlin
tasks.register("buildWithoutTest") {
    dependsOn("assemble")
}
```


---
## К изучению
- [ ] [Understanding Gradle video course](https://www.youtube.com/playlist?list=PLWQK2ZdV4Yl2k2OmC_gsjDpdIBTN0qqkE)
