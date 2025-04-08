---
title: "Паттерн Компоновщик"
tags:
  - pattern
draft: false
---

# Composite pattern

Паттерн __Компоновщик__ объединяет объекты в древовидные структуры для представления иерархий "часть/целое". 
Компоновщик позволяет клиенту выполнять однородные операции с отдельными объектами и их совокупностями.

### Реализация

- Создается интерфейс, позволяющий взаимодействовать с одиночными объектами и с группами таких объектов с помощью единого интерфейса.
- Интерфейс реализуется классом одиночного объекта и классом группы объектов.
- Клиентский код теперь может обращаться с одиночными объектами и с группами одинаково. Это позволяет обходить группы объектов любой вложенности.

![composite](../../images/composite%20pattern.png)

### Случаи использования

Компоновщик используется для обхода дерева/списка объектов.

Некоторые методы в интерфейсе компоновщика могут не иметь смысла для одиночных объектов. 
Поэтому в их реализации в классе одиночных объектов выбрасывается исключение. 
Такие методы нарушают принцип подстановки Барбары Лисков ([Принципы SOLID](../architecture/solid_principles.md)).

### Взаимоотношения с другими паттернами

- [Паттерн Итератор](iterator.md) часто используется для перебора внутри группы объектов.
- [Паттерн Декоратор](decorator.md). 
  Класс группы объектов декорирует класс одиночного объекта. 
  Декоратор обертывает только один объект, а Компоновщик может обертывать группу объектов.

### Примеры

- `RecordInterceptor` с `CompositeRecordInterceptor` в [spring-kafka](../spring/spring_kafka.md)

Как-то я написал такой код для объединения тэгов для различных метрик в приложении, над которым я работал:
```kotlin
sealed interface CustomTags {
    fun toMicrometerTags(): Tags
    fun plus(customTags: CustomTags): CustomTags {
        return when {
            this is CompositeTags && customTags is CompositeTags -> CompositeTags(this.tagSet.plus(customTags.tagSet))
            this is CompositeTags -> CompositeTags(this.tagSet.plus(customTags))
            customTags is CompositeTags -> CompositeTags(customTags.tagSet.plus(this))
            else -> CompositeTags(setOf(this, customTags))
        }
    }
}

private data class CompositeTags(
    val tagSet: Set<CustomTags>
) : CustomTags {
    override fun toMicrometerTags() = tagSet
        .map { it.toMicrometerTags() }
        .reduce { acc, tags -> acc.and(tags) }
}

data class MemberTags(
    val member: String,
    val role: String
) : CustomTags {
    override fun toMicrometerTags() = Tags.of("member", member).and("role", role)
}

data class FlowTags(
    val flow: String
) : CustomTags {
    override fun toMicrometerTags() = Tags.of("flow", flow)
}

data class ReasonTags(
    val status: String,
    val reason: String
) : CustomTags {
    override fun toMicrometerTags() = Tags.of("status", status).and("reason", reason)
}

data class FromToTags(
    val from: String,
    val to: String
) : CustomTags {
    override fun toMicrometerTags() = Tags.of("from", from).and("to", to)
}
```

---
## К изучению
- [X] HeadFirst. Паттерны проектирования. Глава 9
- [X] Статья на Refactoring Guru: https://refactoring.guru/ru/design-patterns/composite
