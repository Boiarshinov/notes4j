---
title: "Служебные нити - Daemon Threads"
tags:
  - multithreading
draft: false
---

JVM продолжает работать пока работает хоть одна нить. Но при этом существует большое количество служебных нитей, которые не обязаны завершаться, чтобы программа закончила исполнение. Это могут быть всевозможные логгеры или, например, GC.

Такие нити называются демонами - Daemon Threads.

Для того чтобы создать нить-демон, нужно у обычной нити до ее запуска вызвать метод `setDaemon(true)`
```java
Thread thread = new LoggerThread();
thread.setDaemon(true);
thread.start();
```

При определении собственных классов, расширяющих Thread, можно устанавливать демоническую сущность в конструкторе:
```java
class MyThread extends Thread{
    public MyThread(){
        setDaemon(true);
    }
    ...
}
```