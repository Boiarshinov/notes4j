---
title: "Fork/Join фреймворк"
tags:
  - multithreading
draft: false
---

Для распараллеливания задач в Java 7 был введен новый класс `ForkJoinTask` (абстрактный), который предоставляет два метода: `fork()` и `join()`, которые позволяют проводить распределенные вычисления немного быстрее.

## Класс `ForkJoinTask`
В этом классе спящие задачи на самом деле не спят, а выполняют другие задачи.
Методы:
- `fork()` - производит асинхронный запуск задачи
- `join()` - дожидается выполнения задачи и возвращает результат ее выполнения

Реализации:
- `RecursiveAction` - (а-ля `Runnable`) используется, когда не нужно считать значения, а нужно просто выполнить какие-либо действия
- `RecursiveTask` - (а-ля `Callable`) используется, когда нужно что-то вернуть

---
## К изучению
- [X] https://habr.com/ru/post/128985/