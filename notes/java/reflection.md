---
title: "Рефлексия - Reflection"
tags:
  - core
draft: false
---

Reflection - это способность класса получать информацию о самом себе.
Для работы с рефлексией существует отдельная библиотека Reflection API.

Рефлексия позволяет:
- Определить класс объекта
- Получить информацию о полях, методах, конструкторах, суперклассе, реализованных интерфейсах
- Получить информацию о модификаторах класса, полей, методов, конструкторов
- Создавать экземпляр данного класса
- Устанавливать значения полей (даже приватных) по их имени
- Вызывать методы объекта (даже приватные) по их имени

Недостатки рефлексии:
- Снижение производительности
- Снижение безопасности - риск раскрытия внутренней информации

---
## Class

Каждый раз, когда Java-машина загружает в память новый класс, она создает новый объект класса `Class`

У каждого класса (и, следовательно, его объектов) есть неявно объявленное статическое поле типа `Class`, в котором хранятся сведения о классе объекта.

Узнать что это за класс можно с помощью метода `getClass()`.

Методы:
- `String getName()`
- `static Class forName(String)` - возвращает класс по имени класса
- `Class getSuperclass()` - возвращает класс родителя
- `Class[] getInterfaces()`
- `Field[] getDeclaredFields()` - возвращает массив полей
- `Field getDeclaredField(String)` - возвращает поле по его названию
- `Method[] getMethods()`
- `Method[] getDeclaredMethods()` - возвращает массив методов
- `Method getDeclaredMethod(String)` - возвращает метод по его имени
- `Method getMethod(String methodName)`
- `Class[] getConstructors()` - возвращает массив конструкторов
- `Class getConstructor(Class[])` - передается массив параметров и по ним возвращается нужный конструктор
```java
Class constructor = clazz.getConstructor({String.class, int.class});
```

- `Object newInstance()` - создает новый объект класса. Для того чтобы создать объект из непустого конструктора нужно использовать следующую конструкцию:
```java
Cat cat = (Cat) Class.forName(Class).getConstructor({String.class, int.class}).newInstance("Snow", 3);
```

- `boolean isAssignableFrom(Class)` - true, если текущий класс является родителем переданного класса или равен ему.

---
## Field
Методы:
- `void setAccessible(boolean)` - устанавливает возможность редактирования поля: true - можно, false - нельзя
- `void set(Object, T)` - устанавливает выбранному объекту, переданному первым аргументом, новое значение, переданное вторым аргументом, для поля, из которого метод был вызван

---
## Method
Метод можно добыть из класса с помощью методов `getMethod(String)` и `getMethods()`
```java
Method m = String.class.getMethod("length");
```

Методы:
- `Object invoke(T t)` - вызывает метод из объекта t.
- `void setAccessible(boolean)` - устанавливает возможность вызова метода: true - можно, false - нельзя
```java
Method m = MyClass.class.getDeclaredMethod("getStringByName", String.class)
m.setAcessible(true);
String result = (String) m.invoke(myClassInstance, "some name");
```

---
## К изучению
- [ ] https://vertex-academy.com/tutorials/ru/reflection-api-v-java-chast1/
- [ ] https://vertex-academy.com/tutorials/ru/reflection-api-v-java-class-field/
- [ ] https://vertex-academy.com/tutorials/ru/reflection-api-v-java-klass-method/