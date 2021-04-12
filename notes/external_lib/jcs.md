---
title: "JCS"
tags:
  - cache
draft: false
---

<mark>JCS -</mark>

В JCS определены 4 базовых типа зон данных:

- зона в оперативной памяти
- зона на диске
- распределенная зона
- удаленная зона

### Зона в оперативной памяти

В зоне работает алгоритм замещения наиболее давнего по использованию элемента. Такой алгоритм называется LRU (Least Recently Used).

Данная зона обладает наилучшей скоростью работы.

### Зона на диске

Представляет собой словарь. В оперативной памяти хранятся ключи, а на диске хранятся данные.

### Распределенная зона

Используется при работе нескольких серверов, каждый из которых хранит свой кэш. При этом могут возникать проблемы несогласованности данных между кэшами серверов.

### Удаленная зона
Кэшированные данные хранятся на удаленном сервере.

---
## Конфигурирование системы JCS
Для конфигурирования системы необходимо создать и заполнить файл `cache.ccf`

В нем указывается максимальное количество объектов в кэше, зоны хранения кэша и пр.
```properties
jcs.default=jcs.default.cacheattributes=[org.apache.jcs.engine.CompositeCacheAttributes](http://org.apache.jcs.engine.CompositeCacheAttributes)

jcs.default.cacheattributes.MaxObjects=1000

jcs.default.cacheattributes.MemoryCacheName=org.apache.jcs.engine.memory.lru.LRUMemoryCache
```

В приведенном примере максимальное количество объектов - 1000, данные хранятся только в зоне оперативной памяти.

ВАЖНО!!! Объекты, которые мы хотим хранить в кэше должны быть [сериализуемы](../java/serialization.md).

---
## Применение JCS
Для получения зоны кэширования по умолчанию:
```java
JCS cache = JCS.getInstance("default");
```

Методы для работы с кэшем:

- `void put(Object key, Object value)` - помещает объект в кэш
- `Object get(Object key)` - возвращает объект из кэша. Если объекта с указанным ключом нет - возвращается null

```java
String key = "holy granade";
String value = "big BOOM";
cache.put(key, value);
String value = (String) cache.get(key);
if (value != null) System.out.println(value);
```

- `void remove(Object key)` - удаляет элемент с указанным ключом из кэша
- `void clear()` - указанная зона кэша очищается от всех данных
- `void dispose()` - указанная зона кэша удаляется

---
## К изучению

- [X] блог IBM: https://www.ibm.com/developerworks/ru/library/j-jcs/index.html