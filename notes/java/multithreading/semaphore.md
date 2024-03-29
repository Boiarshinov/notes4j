---
title: "Семафор"
tags:
  - multithreading
  - concurrency
draft: false
---

# Semaphore

Для контроля одновременного доступа различных потоков к одному ресурсу существует класс `Semaphore`.
Он устанавливает сколько потоков могут одновременно пользоваться ресурсом.
Когда очередной поток запрашивает доступ, то `Semaphore` смотрит: есть ли свободные доступы и выдает его.

Работа `Semaphore` очень похожа на работу rate limiter.

При создании объекта `Semaphore` можно указать количество потоков, которые смогут одновременно пользоваться ресурсом
```java
var semaphore = new Semaphore(int)
```

Также при создании можно указать параметр `fair`.
Если он задан true, то потоки будут получать доступ к ресурсу в порядке очереди, а не рандомно.


Два основных метода:
- `void acquire()` - запрашивается доступ к ресурсу, если доступ пока не разрешен, нить ожидает
- `void release()` - нить освобождает ресурс.

Есть еще дополнительные методы, облегчающие работы с семафором в различных сценариях:
- `void acquire(int)` - можно забрать сразу несколько доступов
- `boolean tryAcquire()`
- `void acquireUninterruptibly`
- `void release(int)` - можно отдать сразу несколько доступов. Причем вернуть можно даже больше, чем брал.

Справочные методы:
- `int availablePermits()` - количество доступов
- `int getQueueLenght()` - количество потоков в очереди
- `List<Thread> getQueueThreads()` - список потоков в очереди
- `int drainPermits()`

## Недостатки
У `Semaphore` очень сложно поддерживать баланс выданных доступов.
Необходимо навешивать кучу `try-finally` блоков, чтобы корректно обработать все кейсы.
```java
try {
  semaphore.acquire();
  try {
    doSomething();
  } finally {
    semaphore.release();
  }
} catch (InterruptedException e) {
  //...
}
```

или можно сделать то же самое с помощью `tryAcquire()`:
```java
boolean permit = false;
try {
  permit = semaphore.tryAcquire(1, SECONDS);
  if (permit) {
    doSomething();
  }
} catch (InterruptedException e) {
  //...
} finally {
  if (permit) {
    semaphore.release();
  }
}
```

В связи с этим `Semaphore` практически никогда не используется.
Вместо него лучше применять [локи](./locks.md) или [блокирующую очередь](../collections/queue.md)

---
## К изучению
- [X] [Курс по многопоточности в Java](https://fillthegaps.getcourse.ru/mt7)
