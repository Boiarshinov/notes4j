---
title: "Триггеры"
tags:
  - database
  - sql
draft: false
---

# Триггеры

**Триггеры** - это операции, которые выполняются автоматически при происхождении каких-либо событий.

Инициирующие операции:
- Вставка
- Изменение
- Удаление

Иногда триггеры могут приводить к тому, что операция, которая этот триггер вызвала, будет отменена

Особенности работы триггеров:
- Могут быть инициированы изменением схемы (не во всех СУБД) или данных
- Могут выполняться для каждой строки или для всего блока
- Возможности использования старых и новых значений (до изменения и после)
- Транзакционность - если при выполнении триггера возникла ошибка, то все изменения будут возвращены к исходному состоянию
- Одни триггеры могут вызывать другие - может привести к stackOverflow или циклическому исполнению триггеров
- Последовательность исполнения - при одновременном задействовании нескольких триггеров, раньше исполняется тот триггер, который был раньше создан

Варианты использования:
- Проверка данных
- Поддержка согласованности
- Журналирование и аудит
- Запуск процедур, несвязанных с обработкой данных

Недостатки:
- Непрозрачность - невозможно понять что происходит при отладке
- Сложность отладки
- Производительность - замедляют работу БД
- Глобальность - нельзя ограничить доступ

---
## К изучению
- [ ] https://habr.com/ru/post/37693/