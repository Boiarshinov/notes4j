---
title: "Паттерн Фабричный метод"
tags:
  - pattern
draft: false
---

Паттерн Фабричный метод заключается в том, что создание определенного типа объекта выносится в абстрактный метод, который по разному реализуется различными наследниками. В остальном работа с созданным объектом ведется на уровне его интерфейса.

Это позволяет отделить логику общей обработки различных видов объектов от их конкретных реализаций.

![factory method](../../images/factory_method_pattern.png)

### Реализация

Класс, который должен работать с разными типами объектов в различных ситуациях делается абстрактным, а для поставки новых экземпляров объектов обработки заводится абстрактный метод. Все наследники этого класса, сделанного абстрактным по своему его реализуют.

Очень важно, чтобы конструкторы, используемые в фабричном методе больше нигде не использовались. Смысл этого паттерна заключается как раз в том, чтобы инкапсулировать принятие решения о том, какой подвид модельного класса создавать.

Иногда метод не делают абстрактным, а по умолчанию предоставляют стандартную реализацию.

### Случаи использования

Паттерн используется, когда необходимо для различных реализаций одного абстрактного класса, поставлять в обработку различные реализации одной модели.

Также этот зачастую используется в библиотечных классах, предназначенных для наследования.

Фабричный метод позволяет соблюсти принцип инверсии зависимостей, абстрагируя работу высокоуровневых компонентов, т.к. они начинают работать с интерфейсами, а не с конкретными реализациями.

Если возможностей фабричного метода начинает не хватать, или код внутри фабричного метода становится слишком сложным, можно перейти к использованию [Паттерн Абстрактная фабрика](abstract_factory.md).

### Примеры из стандартной библиотеки

- `javax.mail` класс `Authenticator`. Каждый его наследник должен реализовать метод `getPasswordAuthenication()`, который поставляет объект с информацией для авторизации на почтовом сервере.

---
## К изучению
- [X] HeadFirst. Паттерны проектирования. Глава 4
- [X] Refactoring Guru: https://refactoring.guru/ru/design-patterns/factory-method