---
title: "Comparator и Comparable"
tags:
  - core
draft: false
---

Эти два интерфейса используются для сравнения объектов в коллекциях.

Для того чтобы объекты определенного класса можно было отсортировать в коллекции, необходимо, чтобы класс реализовывал интерфейс Comparable

## Интерфейс `Comparable<T>`

Данный интерфейс не относится к [функциональным](stream_and_lambda/functional_interface.md) хоть и имеет всего один метод:

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

        //Из-за опасности переполнения int при вызове c большим отрицательным this.height

        // и большим положительным o.height, рекомендуется писать:
        //return Integer.compare(this.height, o.height);
    }
}
```

## Интерфейс `Comparator<T>`

Интерфейс используется для того, чтобы отсортировать объекты в коллекции по определенным критериям, даже если класс объектов не реализует интерфейс `Comparable`.

Интерфейс относится к [функциональным](stream_and_lambda/functional_interface.md)), т.к. имеет всего один метод:

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

`Comparator` отлично заменяется на [Лямбда-выражения](stream_and_lambda/lambda_expressions.md):

```java
Collections.sort(humans, (o1, o2) -> o1.height - o2.height);
```

---
## К изучению

- [X] https://javarush.ru/groups/posts/2262-comparator-v-java