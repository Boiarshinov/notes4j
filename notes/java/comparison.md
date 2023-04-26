---
title: "Comparator и Comparable"
tags:
  - core
draft: false
---

# Comparison
Для сравнения объектов в Java используются два интерфейса: `Comparable` и `Comparator`.

`Comparable` предназначен для наследования.
С помощью его метода `compareTo` объекты, например, будут сортироваться в коллекциях.
`Comparable` совсем не гибок: написав его один раз, объекты всегда будут сортироваться по указанному правилу.
Поэтому, для сравнений объектов между собой по динамическим правилам предназначен [функциональный интерфейс](./stream_and_lambda/functional_interface.md) `Comparator`.

`Comparator` используется для сравнения любых объектов между собой.
Классы этих объектов могут даже не наследовать `Comparable`.
`Comparator` часто используется для того, чтобы отсортировать элементы в коллекции по нужному правилу.


---
## Интерфейс `Comparable<T>`

Данный интерфейс не относится к [функциональным](./stream_and_lambda/functional_interface.md) хоть и имеет всего один метод:

- `int compareTo(T)` - сравнивает переданный объект с вызывающим и возвращает целочисленный результат. При этом должны выполняться следующие условия:
    - если объекты равны, то возвращается 0
    - если данный объект больше чем переданный, то возвращается положительное число
    - если данный объект меньше чем переданный, то возвращается отрицательное число
    - `a.compareTo(b) == - b.compareTo(a)`

```java
class Human implements Comparable<Human>{
    private int height;
    ...
    public int compareTo(Human o){
        return this.height - o.height;
    }
}
```

Чтобы избежать различных краевых случаев с переполнением `Integer.MAX_VALUE` и `Integer.MIN_VALUE` рекомендуется все положительные сравнения сводить к `+1`, а отрицательные к `-1`.
Например, релазация метода `compare` в классе `Integer` выглядит следующим образом:
```java
public static int compare(int x, int y) {
    return (x < y) ? -1 : ((x == y) ? 0 : 1);
}
```


---
## Интерфейс `Comparator<T>`

Интерфейс используется для того, чтобы отсортировать объекты в коллекции по определенным критериям, даже если класс объектов не реализует интерфейс `Comparable`.

Интерфейс относится к [функциональным](./stream_and_lambda/functional_interface.md), т.к. имеет всего один метод:

- `int compare(T o1, T o2)` - возвращает результат сравнения двух объектов в виде целого числа. При этом должны выполнятся следующие условия:
    - если объекты равны, то возвращается 0
    - `a.compareTo(b) == - b.compareTo(a)`
```java
Comparator<Human> compareByHeight = new Comparator<>() {
    public int compare (Human o1, Human o2) {
        return o1.height - o2.height;
    }
}
Collections.sort(humans, compareByHeight);
```

`Comparator` отлично заменяется на [Лямбда-выражения](./stream_and_lambda/lambda_expressions.md):

```java
Collections.sort(humans, (o1, o2) -> o1.height - o2.height);
```

Также в интерфейсе `Comparator` объявлено несколько статических методов, с помощью которых можно просто написать сравнение объектов между собой по определенным полям:
```java
Comparator<Human> compareByHeight = Comparator.comparing(h -> h.getHeight());
```
В метод мы передаем функцию, вытаскивающую из объекта поле, по которому будет проводится сравнение.
Поле должно реализовывать интерфейс `Comparable`.

При этом методы сравнения можно чейнить, чтобы задать нужные правила сортировки (например, если рост двух людей совпал):
```java
// Сортировка последовательно по убыванию роста, убыванию веса и возрастанию возраста.
Comparator<Human> compareComposite = Comparator.comparing(h -> h.getHeight())
    .thenComparing(h -> h.getWeight)
    .thenComparing(Comparator.comparing(h -> h.getAge()).reversed());
```


---
## Размышления
Я считаю, что интерфейс `Comparable` получился неудачным из-за того, что метод `compareTo()` возвращает `int`.
На мой взгляд это сильно сбивает разработчиков с толку.
> Мне нужно вернуть положительное число, если текущее число больше переданного или наоборот?
> Если объект `b` сильно больше объекта `a`, а объект `c` больше объекта `a` совсем чуть-чуть, то должен ли `b.compareTo(a)` возвращать число больше, чем `c.compareTo(a)`?

Можно было бы создать перечисление
```java
public enum Comparison {
  SMALLER, EQUIVALENTIAL, BIGGER;
}
```
и возвращать его.


---
## К изучению
- [X] [JavaRush. Comparator в Java](https://javarush.ru/groups/posts/2262-comparator-v-java)
- [X] [Как написать компаратор](https://t.me/java_fillthegaps/493). Java: Fill the gaps