---
title: "Хэш-таблица"
tags:
  - data_structure
draft: false
---

# Хэш-таблица
Хэш-таблица - это структура данных, основанная на массиве, позволяющая добиться низкой сложности вставки, поиска и удаления за счет использования [хэш-функций](../algorithms/hash_function.md).
В усредненном случае хэш-таблицы позволяют достичь константной сложности `O(n)` для всех основных операций.

Хэш-таблица позволяет реализовать такие абстрактные типы данных, как Set и Map.

## Выбор размера хэш-таблицы
Для того чтобы вероятность коллизий была пренебрежительно мала (1 коллизия на всю таблицу), размер хэштаблицы приблизительно должен быть равен квадрату от количества хранимых элементов.
Но в этом случае объем пустующей памяти становится огромным.

Отношение количества хранимых элементов к размеру массива называется **load factor**.
Эвристическими методами было выведено, что сложность поиска элемента в хэш-таблице будет составлять `O(1)` до значения `load factor = 0.75`.
Рекомендуется при приближении к приведенному значению перестраивать хэш-таблицу, удваивая размер массива.

Желательно, чтобы размер хэш-таблицы был простым числом.
Это уменьшает вероятность неравномерного распределения при выборе неудачной хэш-функции.

---
## Стратегии разрешения коллизий

### Linear Probing
Linear Probing - линейное зондирование. 
В этой стратегии при коллизии мы ищем в массиве следующее свободное место и вставляем значение в него.
При удалении с помощью хэш-функции сначала вычисляется значение ячейки, а затем сравниваются ключи.
Если ключи совпали, то ячейку можно очистить.
Если ключи оказались разными, значит ранее в этой ячейке была коллизия и необходимо, двигаясь вправо, последовательно сравнивать ключи. 
Остановиться можно в тот момент, когда найдется пустая ячейка.
Удаленные ячейки нужно помечать с помощью специального флага, чтобы другие операции удаления не останавливались на этих ячейках.
Если таких tombstone'ов становится слишком много, то необходимо перестраивать хэш-таблицу.

Linear Probing ухудшает сложность всех основных операций до `O(n)`.

### Двойное хэширование
Из-за линейного зондирования данные в хэш-таблицы начинают образовывать группы, которые ухудшают сложность поиска элемента (или свободного места).
Для того чтобы победить этот недостаток, можно использовать двойное хэширование.
Если позиция хэша от ключа уже занята, то новая позиция рассчитывается с помощью новой хэш-функции, использующей результат первой:
```
h2(k) = 1 + ( h1(k) / size ) % (size - 1)
```

### Рандомизированное хэширование
Еще один способ избежания скоплений элементов - это **рандомизированное хэширование**.
В этом случае ключ используется как сид для рандом-генератора.
Если позиция по первому рандомному значению занята, то берем второе и т.д.
Благодаря тому, что ключ используется в качестве сида, последовательность чисел, генерируемая рандомайзером всегда одинаковая для одного и того же ключа.

Это решение было бы очень хорошим, если бы не затраты на создание рандом-генератора для каждого ключа.

### Separate chaining
В ячейке хэш-таблцы можно хранить не вставляемый элемент, а некая коллекция элементов с совпавшими значениями хэшей.
Чаще всего используется односвязный список, но могут использоваться и другие структуры данных, например дерево.
Если при вставке ячейка уже занята, то новый элемент добавляется в начало списка.

Такой способ используется в джавовой `Hashtable`.
В качестве коллекции там используется односвязный список.
В `HashMap` используется более хитрая стратегия - сначала элементы складываются в односвязный список, но когда количество элементов в нем превышает определенное значение, он перестраивается в дерево.

Недостатком Separate Chaining является необходимость хранить дополнительные ссылки и прыгать по этим ссылкам при поиске элемента.

### Cuckoo hashing
В этом случае создается две хэш-таблицы, каждая со своей хэш-функцией.
При вставке нового элемента вычисляется первая хэш-функция от его ключа и определяется позиция в первой таблице.
Если в ней уже что-то было, то предыдущий элемент вытесняется, а вставляемый встает на его место.
Из второго элемента вычисляется хэш-функция второй таблицы и определяется позиция в ней.
Вставляемый элемент снова вытесняет предыдущее значение, которое теперь опять ищет место в первой таблице.
Если выталкивание попадает в цикл, то хэш-таблицы пересоздаются с заменой хэш-функций.

Благодаря тому, что новые значения вытесняют старые, эта стратегия называется кукушкиным хэшированием. 
Птенец кукушки выбрасывает остальные яйца из гнезда, чтобы самому получить больше места.

Кукушкино хэширование позволяет получить константную сложность `O(1)` для поиска и удаления элемента, благодаря тому, что элемент может быть только либо в первой, либо во второй таблице. 
При вставке сложность может достигать `O(n)` из-за потенциальной необходимости пересоздания хэш-таблиц.


### Классификация стратегий
Стратегии разрешения коллизий делятся на группы:
- с открытой адресацией
  * linear probing
  * double hashing
  * random hashing
  * cuckoo hashing
- с закрытой адресацией
  * separate chaining


---
## К изучению
- [X] Лекция 5 курса [Data Structures](https://stepik.org/course/579/syllabus)