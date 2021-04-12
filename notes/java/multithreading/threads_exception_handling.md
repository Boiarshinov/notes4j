---
title: "Обработка исключений в нитях"
tags:
  - multithreading
draft: false
---

Бывает такое, что в нити при выполнении кода выкидывается исключение, при этом сама нить не ловит его. Для того чтобы это исключение поймать и обработать существует специальный интерфейс:

## Интерфейс UncaughtExceptionHandler
Этот интерфейс объявлен внутри класса Thread и вызывается следующим образом:
```java
Thread.UncaughtExceptionHandler handler = new ClassWithUEHImplemented(); //создается объект класса, который реализует интерфейс UEH
```
Интерфейс необходим, чтобы отлавливать ошибки типа Runtime, возникающие в потоках и предпринимать по этому поводу какие-то действия, реализуемые в его единственном методе.

Для того чтобы он обрабатывал неотловленные ошибки, нужно установить его в потоке с помощью метода `setUncaughtExceptionHandler()`
```java
class myThread extends Thread{
    public myThread(){
        setUncaughtExceptionHandler(handler);
        start();
    }
    ...
}
```

Интерфейс имеет один метод, который должен быть реализован в унаследованных классах:

- `void uncaughtException(Thread t, Throwable e)` - этот метод будет вызван, если из нити t, вылетело исключение принадлежащее классу, указанному для e.

Интерфейс является функциональным и поэтому зачастую реализуется как анонимный класс или с помощью лямбда-выражений
```java
Thread thread = new DownloadThread();
thread.setUncaughtExceptionHandler(new Thread.UncaughtExceptionHandler(){
    @Override
    public void uncaughtException(Thread t, Throwable e){
        ...
    }
});
```