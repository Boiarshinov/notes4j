---
title: "Многопоточные коллекции"
tags:
  - collections
  - multithreading
draft: false
---

# Многопоточные коллекции

Существует несколько подходов по созданию коллекций, пригодных к использованию в многопоточной среде:
- `Collections.synchronized*` - синхронизированные версии обычных коллекций
- `Collections.unmodifiable*` - неизменяемые версии обычных коллекций. При создании коллекции в нее передаются все необходимые элементы, а дальше ни убавить, ни прибавить.
- `java.util.concurrent.*` - коллекции, специально предназначенные для работы в многопоточной среде

При выборе необходимо понимать сферу применения каждого из решений.


---
## Synchronized
Чтобы превратить обычную коллекцию в синхронизированную достаточно вызвать:
```java
List<String> syncList = Collections.synchronizedList(myList);
```

К сожалению, не все операции будут синхронизированы.
Например, при прохождении коллекции с помощью итератора нужно будет все равно синхронизироваться самостоятельно:
```java
synchronized(syncList) {
    var iterator syncList.interator();
    while (iterator.hasNext())
        doSomething(iterator.getNext());
}
```
Также, если это кастомная коллекция, то все нестандартные методы нужно будет синхронизировать самостоятельно.

Помимо списка можно синхронизировать и другие коллекции:
- `Collections.synchronizedList()`
- `Collections.synchronizedCollection()`
- `Collections.synchronizedSet()`
- `Collections.synchronizedMap()`
- сортированные множества и мапы


---
## Unmodifiable
В Java 9 добавили фабричные методы в интерфейсы коллекций, позволяющие создать неизменяемую коллекцию:
```java
Set<String> set = Set.of("lol", "kek");
```

Также можно преобразовать существующую коллекцию в неизменяемую:
```java
Set<String> fixedSet = Collections.unmodifiableSet(mySet);
```

При попытке вызова на неизменяемой коллекции методов типа `add()`, `remove()`, будет выброшено исключение `UnsupportedOperationException`.
Такое поведение нарушает [принцип подстановки Барбары Лисков](../../architecture/solid_principles.md), поэтому в более современных языках коллекции делают неизменяемыми по умолчанию, а мутирующие методы выделяют в отдельный интерфейс.
Например в Kotlin: `List` и его наследник `MutableList`.

---
## java.util.concurrent
todo

В коллекциях из данного пакета для увеличения производительности чаще всего блокируются не все данные, а только их часть.
Это позволяет увеличить производительность.

### Fail-safe
Одним из способов защитить коллекции от изменений является Fail-safe подход, в котором при каждом добавлении элемента в коллекцию пересоздается внутренняя структура.
При этом если в момент добавления другой поток итерировался по коллекции, то он продолжит итерироваться по предыдущему состоянию внутренней структуры.
Примером такого подхода являются коллекции:
- `CopyOnWriteArrayList`
- `CopyOnWriteArraySet`

Недостатком таких коллекций является то, что изменения не видны всем потокам сразу.
А также то, что пересоздание внутренней структуры отнимает процессорное время и кушает кучу памяти.

### Lists
У списков есть всего одна реализация в `java.util.concurrent`:
- `CopyOnWriteArrayList`

Если доступ по индексу не нужен, то лучше взять одну из реализаций очереди.
Если список подразумевает частые обновления, то `CopyOnWriteArrayList` не подойдет. 
Вместо него лучше сделать синхронизированный список из обычного.

### Sets
- `CopyOnWriteArraySet`
- `ConcurrentSkipListSet`

Если важен порядок элементов, то подойдет только `ConcurrentSkipListSet`.
Если порядок не важен, то при высокой нагрузке на чтение стоит взять `CopyOnWriteArraySet`, а если основная нагрузка - это запись, то лучше взять `ConcurrentHashMap` и пользоваться его `keySet`.

### Maps
- `ConcurrentHashMap` - нестареющая классика
- `ConcurrentSkipListMap` - предназначена для поиска по диапазонам и нечеткого поиска по ближайшему значению ключа. Сделана на основе структуры данных [Skip List](../../data_structures/skip_list.md). Подобные структуры часто используются в качестве индексов в базах данных, например в [Cassandra](../../database/cassandra.md)

#### ConcurrentHashMap
До того, как в [Java 5](../java_versions.md) появился `ConcurrentHashMap`, все пользовались классом `Hashtable`, в котором все методы были синхронизированы.
В Java 5 появился `ConcurrentHashMap`, но его реализация была далека от идеальной.
Количество бакетов, которые задавались внутренним классом `Segment`, не менялось в зависимости от количества пар ключ-значение, из-за этого при большом количестве элементов мапа становилась узким местом.
Поэтому в Java 8 она была полностью переписана.

Внутри `ConcurrentHashMap` находится некоторое количество бакетов. 
Бакет - это однонаправленный связный список из пар ключ-значение. 
При большом количестве элементов в бакете он преобразуется в дерево.
Бакеты составлены в массив, с размером всегда равным одной из степеней двойки.
При этом мапа старается держать количество бакетов всегда больше, чем в ней есть элементов, чтобы в идеальном случае в каждом бакете было либо один элемент, либо ни одного.

Обновление элементов происходит следующим образом.
Если в бакете всего один элемент, то он обновляется с помощью CAS-операции.
Если в бакете несколько элементов, то верхний из них блокируется, и дальше идет поиск нужного элемента в списке.
Если бакет уже преобразовался в дерево, то блокировать вершину дерева опасно, т.к. дерево может перестроиться, поэтому используются хитрые механизмы, которые хрен поймешь.

Интересно, что если при вставке оказалось, что пора добавлять новые бакеты, то все приходящие потоки вовлекаются в процесс перестроения.

---
## К изучению
- [X] [Курс по многопоточности в Java](https://fillthegaps.getcourse.ru/mt7)