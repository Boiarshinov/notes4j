---
title: "Атомики"
tags:
  - core
  - concurrency
draft: false
---

<mark>//Написать подробно зачем нужны и в каких случаях используются
</mark>

Атомарные классы содержатся в пакете `java.util.concurrent.atomic`.
Атомарные классы реализуют оптимистический подход к блокировке.
Классы:

- Примитивы (и не только)
    - `AtomicBoolean`
    - `AtomicInteger`
    - `AtomicLong`
    - `AtomicReference`
- Массивы
    - `AtomicIntegerArray` - для int
    - `AtomicLongArray` - для long
    - `AtomicReferenceArray` - для ссылок на объекты
- Обновление полей через [рефлексию](reflection.md)
    - `AtomicIntegerFieldUpdater`
    - `AtomicLongFieldUpdater`
    - `AtomicReferenceFieldUpdater`
- Классы для реализации некоторых алгоритмов
    - `AtomicStampedReference`
    - `AtomicMarkableReference`

---

## К изучению

- [X] https://metanit.com/java/tutorial/8.9.php
- [X] http://java-online.ru/concurrent-atomic.xhtml