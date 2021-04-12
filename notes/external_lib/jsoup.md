---
title: "Jsoup"
tags:
  - web
  - external_lib
draft: false
---

**Jsoup** - это open-source библиотека для работы с HTML.

Функции Jsoup:

- Очистка и разбор HTML из URL, файла или строки
- Нахождение и извлечение данных с помощью DOM или CSS селекторов
- Манипулирование HTML элементами, атрибутами и текстом

Для использования Jsoup необходимо импортировать библиотеку org.jsoup при сборке приложения в Maven

---
## Класс Jsoup

Методы:
- `static Document parse(String)` - разбирает html документ, переданный в виде строки
- `static Document parse(URL)` - разбирает html документ, находящийся по переданному URL
- `static Document parse(File)` - разбирает html документ из файла.