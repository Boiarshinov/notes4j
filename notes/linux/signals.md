---
title: "Signals"
tags:
  - linux
draft: false
---

# Signals

Сигналы - это один из способов взаимодействия [процессов](./processes.md) Linux между собой.
Когда процесс получает сигнал, то происходит прерывание и процесс пытается обработать сигнал.

Каждый процесс устанавливает обработчик сигналов.
Некоторые сигналы могут игнорироваться, другие обрабатываться по своему.
Если обработка какого-либо сигнала не переопределена процессом, то используется обработчик по умолчанию.

Сигналов в Linux ограниченное количество (не более 64).
У каждого сигнала есть свой порядковый номер и название.

Основные сигналы:
- SIGINT (2) - signal interruption - это вежливый запрос на завершение процесса. Процесс должен отпустить все ресурсы, закрыть все соединения и завершиться. Такой сигнал отправляется процессу при нажатии в оболочке терминала сочетания клавиш `Ctrl + C`.
- SIGKILL (9) - signal kill - жесткая остановка процесса. При получении сигнала процесс должен завершиться сразу же, не прибирая за собой ресурсы. При этом некоторые ресурсы могут остаться в неконсистентном состоянии, поэтому такой сигнал можно посылать только в экстренных случаях.
- SIGTERM (15) - signal termination - так же, как и SIGINT вежливый запрос на graceful shutdown. Такой сигнал отправляется процессу от другого процесса.

Отправить сигнал процессу можно с помощью команды `kill`:
```bash
kill -15 1814
```

Здесь `15` - это порядковый номер сигнала, а `1814` - PID процесса.

Также с помощью команды `kill` можно ознакомиться со всеми сигналами, которые поддерживает ваша ОС:
```bash
kill -l
```

---
## К изучению

- [X] Статья в [Wikipedia](https://ru.wikipedia.org/wiki/%D0%A1%D0%B8%D0%B3%D0%BD%D0%B0%D0%BB_(Unix))
- [ ] [Статья о сигналах в Linux](https://faculty.cs.niu.edu/~hutchins/csci480/signals.htm) на английском
- [X] [Разница между SIGINT, SIGKILL и SIGTERM](https://eitca.org/cybersecurity/eitc-is-lsa-linux-system-administration/linux-processes/process-signals/examination-review-process-signals/explain-the-difference-between-sigint-sigterm-and-sigkill-signals-in-linux)