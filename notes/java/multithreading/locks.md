---
title: "Локи"
tags:
  - multithreading
  - concurrency
draft: false
---

# Локи - замки

__Локи__ - это инструменты, позволяющие ограничить доступ к данным и коду из разных потоков.
Самым примитивным локом в Java является ключевое слово `synchronized`.
Но оно не давала достаточной гибкости при работе с многопоточкой, поэтому в [Java 1.5](../java_versions.md) были добавлены различные реализации замков.
Интересно, что на выходе Java 1.5 замки были намного менее производительными, чем `synchronized`, но уже в Java 1.6 производительность улучшили, и теперь `synchronized` и `ReentrantLock` выдают почти одинаковые результаты.

Самым базовым интерфейсом замков является `Lock`.

Методы:
- `void lock()` - закрывает замок (блокирует доступ к переданному объекту). Если доступ заблокирован, то метод будет ждать разблокировки (возможно вечно)
- `void unlock()` - открывает замок (разблокирует доступ к переданному объекту). Рекомендуется открывать замок в finally блоке

Типичный пример использования:
```java
private Lock lock = ...;

void method() {
  lock.lock();
  try {
    doSomething();
  } finally {
    lock.unlock();
  }
}

```

- `void lockInterruptibly()` - закрывает замок. Но если поток, захвативший замок, будет прерван, то замок откроется автоматически.
- `boolean tryLock()` - пробует в момент вызова закрыть замок, если получается, то возвращает true. Часто используется, чтобы тред не попал в дедлок. 
- `boolean tryLock(long time, TimeUnit unit)` - пробует закрыть замок через указанное время, если получается, то возвращает true
- `Condition newCondition()` - todo. Про `Condition` см. ниже

В стандартной библиотеке у `Lock` только одна реализация: `ReentrantLock`.
Хотя в некоторых местах стандартной библиотеки встречаются внутренние классы с другой реализацией.


Виды локов:
- `ReentrantLock`
- `ReentrantReadWriteLock`
- `StampedLock`


---
## ReentrantLock
`ReentrantLock` - самый простой лок.
Является более гибкой версией `synchronized`.

Внутри `ReeentrantLock` устроен похоже на монитор.
У него есть счетчик вхождений и идентификатор потока, которому в данный момент предоставлен доступ.

`ReentrantLock` может быть создан двух типов:
- fair - потокам, ожидающим открытия замка, будут выдаваться доступ в том порядке, в котором они пришли.
- unfair - после открытия замка доступ выдается случайном потоку.

По умолчанию создается unfair версия.
```java
Lock unfairLock = new ReentrantLock();
Lock fairLock = new ReentrantLock(true);
```


---
## Condition
Интерфейс `Condition` нужен для того, чтобы дать при работе с локами те же возможности, что дают методы класса `Object`: `wait()`, `notify()`, `notifyAll()`.

Экземпляр `Condition` можно получить только из объекта лока.

- `void await()` - 
- `void signal()` - 
- `void signalAll()` - 

`Condition` используется к примеру в `ArrayBlockingQueue`.


---
## ReadWriteLock
Очень часто замки используются в классах, чтобы обеспечить атомарность чтения и записи и избавиться от грязного чтения.
Но если использовать `ReentrantLock`, то одновременно читать сможет только один поток, что может быть неэффективно, когда частота чтения сильно превышает частоту записи.
Для этих случаев служит `ReadWriteLock`.

В `ReadWriteLock` находится сразу два замка: один на запись, другой на чтение. 
Получить их экземпляры можно с помощью методов:
- `Lock writeLock()` - этот замок дает эксклюзивный доступ к объекту. Пока он занят, нельзя даже читать.
- `Lock readLock()` - пока writeLock не занят, зайти могут любые потоки в неограниченном количестве

Интерфейс `ReadWriteLock` имеет единственную реализацию в стандартной библиотеке: `ReentrantReadWriteLock`.

`ReentrantReadWriteLock` не очень удачно написан, из-за чего при большой нагрузке на чтение поток, пытающийся записать что-либо, может голодать, не получая доступ к ресурсу.
Поэтому в продуктовом высоконагруженном коде `ReentrantReadWriteLock` лучше не использовать!

---
## Stamped Lock
Появился в Java 8.
Достаточно странная реализация. 
У него нет свойства Reentrancy.
Зато он добавляет к чтению и записи еще один режим - [оптимистическое](./threadsafe.md) чтение.

Внутри `StampedLock` при каждой записи запоминается отметка времени.
При оптимистической блокировке запоминается отметка времени последней записи, а при снятии блокировки изначальное значение сравнивается с текущим.
Если они не совпадают, то операцию, основанную на оптимистической блокировке, можно повторить.



---
## К изучению

- [ ] https://metanit.com/java/tutorial/8.9.php
- [ ] (http://java-online.ru/concurrent-atomic.xhtml)  https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/Lock.html
- [X] Про Lock очень доступно: https://www.youtube.com/watch?v=s032s29-NUU&list=PL6jg6AGdCNaXo06LjCBmRao-qJdf38oKp
- [ ] [Устройство ReentrantLock](https://programmer.help/blogs/implementation-principle-of-reentrantlock.html)
- [ ] [Сравнение ReentrantLock и synchronized](https://web.archive.org/web/20210414205911/https://blogs.oracle.com/dave/javautilconcurrent-reentrantlock-vs-synchronized-which-should-you-use)
- [X] [Курс по многопоточности в Java](https://fillthegaps.getcourse.ru/mt7)
- [ ] [StampedLock. Видеозапись доклада](https://www.youtube.com/watch?v=c3_j5cPcRAI&ab_channel=Devoxx)
