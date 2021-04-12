---
title: "Collectors - Коллекторы"
tags:
  - java_8
  - collections
  - functional_programming
draft: false
---

Коллекторы позволяют собрать элементы стрима в одну или несколько коллекций.

Методы:

- сборка всех элементов в стандартные коллекции:
    - `toList()` - собирает элементы в ArrayList
    - `toSet()` - собирает элементы в HashSet
    - `toMap(Function<T, K>, Function<T, V>)` - собирает элементы в HashMap
```java
Map<Integer, String> shortInfoTestMaterials = testMaterials.stream()
    .collect(Collectors.toMap(TestMaterial::getId, TestMaterial::getName));
```
-
    - `toMap(Function<T, K>, Function<T, V>, BinaryOperator<V>)` - собирает элементы в HashMap, при этом определяет что делать со значениями, если их ключи совпадают.
- `toCollection(Supplier<Collection>)` - собирает элементы в переданную коллекцию. Тип коллекции должен совпадать с типом элементов
- `partitioningBy(Predicate<T>)` - собирает элементы в словарь с двумя элементами, в котором ключами являются значения true и false, а значениями являются списки элементов удовлетворяющих или не удовлетворяющих условию соответственно.
```java
Map<Boolean, List<TestMaterial>> partirion = testMaterials.stream()
    .collect(Collectors.partitioningBy(TestMaterial::isMarked));
```

- `groupingBy(Function<T, K>)` - собирает элементы в мапу. В этой мапе ключами являются результаты выполнения переданной функции, а значениями являются списки элементов, у которых функция вернула одинаковый результат.
```java
Map<TestMaterialType, List<TestMaterial>> partirion = testMaterials.stream()
    .collect(Collectors.groupingBy(TestMaterial::getType));
```

- коллекторы, возвращающие единственное значение:
    - `summingInt(ToIntFunction<T>)` - преобразует элементы стрима в int и суммирует все значения
    - `summingLong(ToLongFunction<T>)` - то же для long
    - `summingDouble(ToDoubleFunction<T>)` - то же для double
    - `averagingInt(ToIntFunction<T>)` - преобразует элементы стрима в int и вычисляет среднее значение (в результате возвращается double)
    - `averagingLong(ToLongFunction<T>)` - то же для long
    - `averagingDouble(ToDoubleFunction<T>)` - то же для double
    - `maxBy(Comparator<T>)` - возвращает максимальный элемент из стрима, сравнивая элементы с помощью переданного компаратора
    - `minBy(Comparator<T>)` - возвращает минимальный элемент из стрима, сравнивая элементы с помощью переданного компаратора
    - `counting()` - возвращает количество элементов
    - `reducing(T, BinaryOperator<T>)` - собирает все элементы коллекции во что-то одно большое и красивое. Принимает первоначальный элемент коллекции и оператор, который будет складировать элементы.
    - `reducing(BinaryOperator<T>)`
    - `reducing(U, Function<T, U>, BinaryOperator<U>)` - то же, но кроме того передается функция, которая преобразует элементы в другой тип.

---
## Низшие коллекторы (wat? низшие?)

Некоторые методы имеют перегруженные версии, которые позволяют использовать внутри них другие коллекторы.

- `groupingBy(Function<T, K>, Collector<T, A, D>)` - собирает элементы в словарь, в котором ключами являются результаты выполнения переданной функции, а значениями являются результаты свертки другими коллекторами.

---
## К изучению:
- [ ] javadoc на класс Collectors
- [X] Курс на Stepik по функциональному программированию (на англ.): https://stepik.org/course/1595/syllabus
- [X] Р. Уорбэртон - Лямбда-выражения в Java 8