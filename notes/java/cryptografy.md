---
title: "Криптография в Java"
tags:
draft: false
---

## Класс MessageDigest

Класс используется для того, чтобы получать дайджесты из массивов байт. Класс объявлен в пакете `java.security`.

Конструкторы
- их нет, используется статический метод MessageDigest getInstance(String) - возвращается объект, способный создавать дайджесты по одному из алгоритмов, тип которых передается в параметре:
    - MD2
    - MD5
    - SHA-1
    - SHA-256 - рекомендуется к использованию этот и ниже
    - SHA-384
    - SHA-512

Методы:
- `byte[] digest(byte[])` - преобразует переданный набор байт в дайджест
- `void update(byte[])` - записывает набор байт в дайджест, но пока еще не возвращает его
```java
byte[] data = "Very important information".getBytes();
MessageDigest messageDigest = MessageDigest.getInstance("MD5");
byte[] digest = messageDigest.digest(data);
```

---

## К изучению
- [X] Про MessageDigest: https://habr.com/ru/post/444974/