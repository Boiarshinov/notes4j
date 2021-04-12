---
title: "Паттерн Singleton"
tags:
  - pattern
draft: false
---

Singleton - это класс, который позволяет создать только единственный объект. Любые обращения к классу будут возвращать этот объект.
```java
class Singleton{

    private static Singleton instance; //Поле, в котором будет храниться единственная реализация класса

    private Singleton(){} //Приватный конструктор
    public static Singleton getInstance() {
        if (instance == null) return new Singleton();
        return instance;
    }
}
```

Существует понятие ленивая инициализация - это значит, что объект класса будет создан только тогда, когда к нему впервые обратятся. Фрагмент кода выше представляет как раз ленивую инициализацию.

Для того чтобы при работе в условиях многопоточности при любых условиях создавался только один объект класса-одиночки, необходимо инициализировать объект в синхронизированном блоке
```java
class Singleton{
    private static Singleton instance;
    static {
        synchronized {
            instance = new Singleton();
        }
    }
    private Singleton(){} //Приватный конструктор
    public static Singleton getInstance() {
        return instance;
    }
}
```

Недостатком такого подхода является увеличение времени старта приложения. Для приложений, запущенных на сервере - это нормально, а для мобилок нежелательно.

Идеальный способ, сочетающий потокобезопасность с ленивой инициализацией и быстродействием.
```java
class Singleton{
    private volatile static Singleton instance;

    private Singleton(){} //Приватный конструктор

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

## К изучению
- [X] HeadFirst. Паттерны проектирования. Глава 5