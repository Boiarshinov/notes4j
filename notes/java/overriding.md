---
title: "Перегрузка"
tags:
  - core
  - syntax
draft: false
---

Перегрузка - добавление нового метода с названием, которое уже имеется, но с другими аргументами или типов возвращаемого значения.

Можно:

- изменять количество аргументов
```java
public int sum(int a, int b){ //2 аргумента
    return a + b;
}
public int sum(int a, int b, int c); //3 аргумента
    return a + b + с;
}
```

- менять тип аргументов
```java
public int sum(int a, int b){ //здесь аргументы типа int
    return a + b;
}
public int sum(String a, String b); //а тут типа String
    return Integer.parseInt(a) + Integer.parseInt(b);
}
```

- менять тип возвращаемого значения и тип аргументов
```java
public int sum(int a, int b){ //здесь аргументы типа int
    return a + b;
}
public double sum(double a, double b); //а тут типа double
    return a + b;
}
```

- изменять уровень доступа
```java
public int sum(int a, int b){ //здесь public
    return a + b;
}
private double sum(double a, double b); //а тут private
    return a + b;
}
```

Нельзя делать все, что не позволит компилятору определить какой из методов нужно вызвать. Например:

- Менять только тип возвращаемого значения
- Менять только уровень доступа
- Менять только бросаемые исключения