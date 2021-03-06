---
title: "Мапы - Map"
tags:
  - core
  - collections
draft: false
---

Мапы относятся к [коллекциям](collections.md).

При этом мапы не реализуют интерфейс `Collection`, из-за существенного отличия в контрактах.

У мап есть множество названий: словарь, карта, отображение. "Словарями" они называются благодаря существованию со времен Java 1 абстрактного класса `Dictionary`, ныне являющегося устаревшим.

Мапа представляет собой множество элементов, хранящихся по уникальному названию - ключу. *Ключи не могут повторяться!* Добавление элемента с ключом, который уже есть в коллекции, перезапишет этот элемент.

Контракт всех мап определяется интерфейсом `Map`.
Пару ключ-значение представляет интерфейс `Entry`.

### Основные представители

- `HashMap` - самая распространенная реализация
- `LinkedHashMap` - складывает объекты в порядке их добавления в словарь
- `TreeMap` - предоставляет структуру в виде дерева, объекты сортируются по ключу
- `Hashtable` - устаревшая реализация. Вместо нее рекомендуется использовать `HashMap`. На `Hashtable` основан известный класс `Properties` (про работу с ним см. [Файл параметров](../properties.md))

<mark>//вставить картинку с диаграммой наследования</mark>

<mark>//расписать про SortedMap, NavigableMap, IdentityHashMap и прочих</mark>

---

## Интерфейс `Map`

Методы:
- `int size()` - возвращает размер отображения (количество пар)
- `boolean isEmpty()` - проверка на пустоту
- `boolean containsKey(key)` - проверить наличие ключа
- `boolean containsValue(value)` - проверить наличие значения
- `V get(Object key)` - получить значение по ключу
- `V put(K key, V value)` - добавить пару. Если уже существует элемент с ключом key, то он его значение изменится на value
- `V remove(Object key)` - удалить пару по ключу
- `void clear()` - очистить коллекцию
- `Set<Map.Entry<K, V>> entrySet()` - получить множество всех пар
- `Set<K> keySet()` - получить множество всех ключей
```java
Set<Integer> keys = myMap.keySet(); //сохраняем в Set, т.к. не может быть повторяющихся значений
```
- `Collection<V> values()` - получить множество всех значений
```java
ArrayList<String> values = myMap.values(); //сохраняем в ArrayList, т.к. могут быть повторяющиеся значения
```
- `void forEach(BiConsumer<T, U>)` - для каждого элемента коллекции совершает какие-то действия, определенные в переданном функциональном интерфейсе. Здесь можно использовать [Лямбда-выражения](../stream_and_lambda/lambda_expressions.md).

---

## Хэшмапа `HashMap`

Хэшмапа - это такой тип мапы, который старается обеспечить константное время для извлечения объекта по ключу. Хэшмапа не гарантирует соблюдения порядка хранимых элементов.

Реализации хэшмап есть во всех языках программирования. В Java такой реализацией является `HashMap`.

Работа хэшмапы основывается на распределении элементов по так называемым бакетам (bucket - ведро) в зависимости от значения хэш-функции от ключа.

Когда в хэшмапу приходит новый элемент, у его ключа вычисляется хэшкод, от которого затем вычисляется хэш-функция (реализация задается самой хэшмапой). Если в хэшмапе нет бакета с таким хэшом, то создается новый бакет (общее количество бакетов ограничено размером массива, массив увеличивается в размере только при перестроении хэшмапы). Если бакет с таким хэшом уже существует, то элемент записывается в этот бакет. При этом хэшмапа оценивает количество элементов с коллизиями хэшей и может решить перестроиться. Таким образом вставка элемента в хэшмапу осуществляется за константное время O(1).

При получении из хэшмапы значения по ключу, от ключа вычисляется хэш (также через хэшкод и хэш-фукцию). По хэшу определяется в каком бакете находится элемент и затем элементы в бакете итерируются пока не будет найден элемент с совпадающим ключом. Таким образом получение элемента по ключу осуществляется за константное время, но в худшем случае за O(n) - когда все элементы попали в один бакет (При большом количестве элементов за O(log(n)), почему см. ниже).

Работа хэшмапы зависит от двух параметров: количества бакетов и коэффициента нагрузки (load factor). Когда количество элементов в любом бакете превышает произведение количества бакетов на коэффициент нагрузки, хэшмапа перестраивается, увеличивая количество бакетов примерно вдвое.

По умолчанию при создании хэшмапы в ней создается 16 бакетов, а коэффициент нагрузки равен 0.75. Это значит, что хэшмапа будет впервые перестроена, когда в один из бакетов будет добавлен 13-й элемент.

Значения стартового количества бакетов и коэффициента нагрузки можно задать при инициализации мапы:
```java
final Map<Long, MyClass> objectById = new HashMap<>(10, 0.8f);
```
При увеличении коэффициента нагрузки хэшмапа будет реже перестраиваться, но при этом поиск по ней будет дольше.

Хэшмапа не обеспечивает защиты от конкурентного доступа. Для таких случаев нужно пользоваться `ConcurrentHashMap`.

Хэшмапа позволяет хранить null значения и использовать null в качестве ключа.

Элементы в `HashMap` хранятся в виде массива элементов `Node`. Именно `Node` и является бакетом.  Класс `Node` является односвязным списком (хотя и не реализует интерфейс `List`). Каждый элемент в нем хранит ссылку на следующий элемент.

Начиная с Java 8 когда элементов в связном списке становится слишком много, его реализация подменяется на красно-черное дерево, представленное наследником класса `Node` - `TreeNode`.  Благодаря этому при большом количестве коллизий в худшем случае поиск по ключу занимает не O(n), а O(log(n))

Если класс ключей реализует интерфейс `Comparable`, то для ускорения сравнения ключей между собой используется сравнение с помощью `compareTo()` вместо вычисления хэша каждого ключа.

---
## Потокобезопасная `ConcurrentHashMap`
В ConcurrentHashMap элементы хранятся не единым куском, а в некотором количестве сегментов (по умолчанию 16).

Для каждого контейнера есть свой замок (Lock), который блокирует определенную часть словаря для обработки другими нитями

Поля:
- `concurrencyLevel` - количество сегментов, на которые будет разбита карта
- `initialCapacity` - количество элементов, которые

Конструкторы:
- `ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel)`

Методы:
- `V putIfAbsent(T key, V value)` - если по переданному ключу еще нет значений, то записывает значение в словарь и возвращает null. Если значение по такому ключу уже есть, то возвращает текущее значение, перезапись значения не производится.

---
## Древовидная `TreeMap`

Хранит пару ключ + значение любых типов, при этом автоматически сортируется по ключу от меньшего к большему.

Удобно использовать, когда необходимо сортировать по ключу.

---
## К изучению:
- [X] Про HashMap: https://habr.com/ru/post/128017/
- [ ] Про HashMap с учетом изменений Java 8: https://habr.com/ru/post/421179/
- [X] Про ConcurrentHashMap: https://habr.com/ru/post/132884/
- [ ] Про ConcurrentHashMap: http://poltora.info/ru/blog/kak-ispolzovat-concurrenthashmap-v-java/
- [X] Про LinkedHashMap: https://habr.com/ru/post/129037/