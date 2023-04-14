---
title: "Атомики"
tags:
  - core
  - concurrency
draft: false
---

# Атомики

Атомарные классы содержатся в пакете `java.util.concurrent.atomic`.
Атомарные классы реализуют оптимистический подход к блокировке.

Атомики реализуют CAS-операции (compare-and-swap).
Такие операции позволяют безопасно обновлять модифицировать данные в многопоточной среде без блокировок.
```java
private AtomicInteger value = new AtomicInteger();

void updateValue() {
  int initial, newValue;
  do {
    initial = value.get();
    newValue = calculate(initial);
  } while (!value.compareAndSet(initial, newValue));
}
```

Следует помнить, что:
- Одновременно можно безопасно обновить только одну переменную
- Внутри цикла, обновляющего переменную не должно быть сайд-эффектов


Атомики подойдут для:
- Статистика:
  - Счётчики
  - min/max
  - latest value
- Неблокирующие алгоритмы

Классы:
- Примитивы
    - `AtomicBoolean`
    - `AtomicInteger`
    - `AtomicLong`
- Ссылки
    - `AtomicReference` - атомарная ссылка на объект
    - `AtomicMarkableReference` - атомарная ссылка с флажком. Удобно использовать для тех объектов, которые долго создаются, а также для тех, которые надо пометить на удаление
    - `AtomicStampedReference` - атомарная ссылка с чиселкой. В чиселке можно хранить timestamp или версию объекта. Предназначен для решения ABA проблемы
- Массивы - обертки позволяют атомарно работать с каждым элементом
    - `AtomicIntegerArray` - для int
    - `AtomicLongArray` - для long
    - `AtomicReferenceArray` - для ссылок на объекты
- Обновление полей через [рефлексию](../reflection.md)
    - `AtomicIntegerFieldUpdater`
    - `AtomicLongFieldUpdater`
    - `AtomicReferenceFieldUpdater`
- Атомарные счетчики - рекомендуется использовать вместо Atomic-оберток над примитивами в случаях высокой конкурентности
    - `LongAdder`
    - `DoubleAdder`
- Атомарные накопители - аналог счетчиков, но вместо сложения в них можно передать любую функцию
    - `LongAccumulator`
    - `DoubleAccumulator`

---
## Устройство Atomic классов
Внутри Atomic класса содержится поле, помеченное модификатором `volatile`.
Но одного `volatile` недостаточно, для работы с ним используются нативные операции, объявленные в `Unsafe`.

Из-за наличия расходов на дополнительные вызовы, атомики работают чуть медленнее обычных `volatile` полей.

---
## Подробный обзор Atomic классов
### AtomicInteger
Такие обертки обычно содержат методы для выполнения CAS операций:
- `boolean compareAndSet(expected, newValue)` - пытается обновить текущее значение новым и возвращает `true` при успехе
- `int compareAndExchange(expected, newValue)` - пытается обновить текущее значение новым и возвращает текущее значение

Геттеры и сеттеры:
- `int getAndSet(newValue)` - 
- `void set(newValue)` - 
- `int get()` - 
- `int intValue()` - 

Атомарные обновления:
- `int incrementAndGet()` - атомарный аналог `++i`
- `int getAndIncrement()` - атомарный аналог `i++`
- `int decrementAndGet()` - атомарный аналог `--i`
- `int getAndDecrement()` - атомарный аналог `i--`
- `int addAndGet(delta)` - обновить на заданную дельту и вернуть получившиеся значение
- `int getAndAdd(delta)`
- `int accumulateAndGet(x, accFunc)`
- `int updateAndGet(udpFunc)` - применяет в внутреннему значению переданную функцию
Передаваемые функции не должны содержать сайд-эффектов, т.к. из-за особенностей в реализации они могут быть выполнены несколько раз.

Атомарные обертки над примитивами подходят для ситуаций:
- `read-modify-write`-паттерн
- при низкой конкуренции между потоками. При высокой конкурентности лучше использовать `LongAdder`.

Внутри операций атомарного обновления используется spin lock принцип:
```java
do {
    v = getIntVolatile(...);
} while (!compareAndSetInt(v, v + 1));
```
При высокой конкурентности потоки будут постоянно расходовать процессорное время, зависая в бесконечном цикле.
Поэтому для высокой конкурентности лучше использовать `LongAdder`.

### LongAdder
Внутри LongAdder помимо `volatile` переменной используется массив, в котором каждая ячейка принадлежит своему потоку.
Если поток не смог обновить значение переменной, то он записывает значение в свою ячейку массива.

- `void increment()`
- `void decrement()`
- `void add(x)` - добавляет указанное значение
- `long sum()` - возвращает текущее значение счетчика
- `long sumThenReset()`
- `void reset()` - сбрасывает значение счетчика

`LongAdder` выгодно использовать когда значение часто обновляется и редко читается.



---
## К изучению
- [X] https://metanit.com/java/tutorial/8.9.php
- [X] http://java-online.ru/concurrent-atomic.xhtml
- [X] [Курс по многопоточности в Java](https://fillthegaps.getcourse.ru/mt7)