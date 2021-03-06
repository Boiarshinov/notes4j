---
title: "Семантическое версионирование - SemVer"
tags:
draft: false
---

**Семантическое версионирование** - это стандартизованный способ выбора названия версии продукта.

Предназначено в том числе для автоматического парсинга машинным способом.

![](https://habrastorage.org/files/b67/0f8/e31/b670f8e3171c4f5fb7123e930794c6cd.png)

В соответствии со стандартом название версии должно включать:

- Обязательно:
    - Мажорную версию
    - Минорную версию
    - Патч-версию
- Опционально
    - предрелизная версия (обычно это alpha, beta или rc - release candidate) - обычно это указывает на то, что версия нестабильна.
    - метаданные системы сборки

Правила инкрементирования номеров версий:

- Номер мажорной версии увеличивается при изменениях в API, ломающих обратную совместимость
- Номер минорной версии увеличивается при добавлении новых фич, при этом обратная совместимость должна сохраняться.
- Номер патч-версии увеличивается при небольших изменениях внутри проекта, не изменяющих и не добавляющих нового API. Сюда могут быть отнесены исправления багов текущей минорной версии, проведение рефакторинга.

При увеличении номера вышестоящей цифры, нижестоящие номера сбрасываются в ноль: 3.14.5 -> 3.15.0 -> 3.15.1 -> 4.0.0

Начальные версии продукта, API которого постоянно меняется, должен называться с 0 в мажорной версии: 0.8.16

Предрелизная версия обозначается в соответствии со следующим паттерном (записано в виде регулярного выражения):
```
-[0-9a-zA-Z]+(.[0-9a-zA-Z]+)*
```

Метаданные обозначаются так же, как и предрелизная версия, но в начале вместо дефиса - плюс.

Метаданные игнорируются системами сборки версий.

---
## К изучению

- [X] Официальный сайт стандарта: https://semver.org/lang/ru/
- [X] Все, что вы хотели знать о семантическом версионировании (2016 г.): https://habr.com/ru/company/Voximplant/blog/281593/