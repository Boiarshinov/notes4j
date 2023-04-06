---
title: "Генераторы случайных чисел - рандомайзеры"
tags:
  - core
draft: false
---


# Random

Существует несколько способов получения случайных чисел в Java:
- Метод `Math.random()` - сахарочек
- Класс `Random` - несравненная классика
- Класс `ThreadLocalRandom`
- класс `SecureRandom` - наследник `Random` с чуть более сложным алгоритмом
- класс `SplittableRandom` - очень быстрый, с более равномерным распределением, чем у `Random`, но при этом НЕ потокобезопасный


### Метод `Math.random()`

Метод возвращает случайное вещественное число double со значением от 0 включительно до 1 не включая.
Под капотом используется тот же класс `Random`.


### Класс `Random`
Конструкторы:
- `Random()`

Методы:
- `int nextInt(int)`
- `long nextLong(long)`
- `double nextDouble()`
- тысячи их

Является потокобезопасным.
Для атомарности изменения внутреннего состояния под капотом используется оптимистическая блокировка с помощью CAS-операций.
```java
protected int next(int bits) {
    long oldseed, nextseed;
    AtomicLong seed = this.seed;
    do {
        oldseed = seed.get();
        nextseed = (oldseed * multiplier + addend) & mask;
    } while (!seed.compareAndSet(oldseed, nextseed));
    return (int)(nextseed >>> (48 - bits));
}
```
Из-за этого `Random` может недостаточно хорошо работать в высококонкурентной среде.
В высококонкурентной среде лучше пользоваться `ThreadLocalRandom`.


## Класс `ThreadLocalRandom`

Для каждого потока `ThreadLocalRandom` хранит базовое значение, доступ к которому происходит через Unsafe метод. 
На основе базового значения локально вычисляется "случайное" число. 
Базовое значение обновляется при каждой итерации. 

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