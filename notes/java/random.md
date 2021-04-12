---
title: "Генераторы случайных чисел - рандомайзеры"
tags:
  - core
draft: false
---


Существует несколько способов получения случайных чисел:

- метод `math.random()`
- использование методов класса `Random`
- использование методов класса `ThreadLocalRandom`

**Метод Math.random()**

Метод возвращает случайное вещественное число double со значением от 0 включительно до 1 не включая.

**Класс Random**
Конструкторы:
- `Random()`

Методы:
- `int nextInt(int)`
- `long nextLong(long)`
- `double nextDouble()`
- тысячи их

**Класс ThreadLocalRandom**

Класс позволяет изолировать генерацию случайных чисел между потоками

Конструкторы:
- их нет. Объект класса вызывается следующим образом:

```java
ThreadLocalRandom.current().nextInt(100); //С помощью единственного статического метода current()
```

Методы:
- `int nextInt(int)`
- `long nextLong(long)`
- `double nextDouble()`
- тысячи их