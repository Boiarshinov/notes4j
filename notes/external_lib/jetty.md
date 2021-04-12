---
title: "Jetty"
tags:
  - web
  - external_lib
draft: false
---

**Jetty** - это http-сервер
![jetty](../../images/jetty.png)

Части jetty-сервера:

- `Connector` - принимает запросы от браузера
- `ThreadPool` - Executor нитей. Содержит в себе несколько нитей, в которые могут быть положены необходимые инструкции для выполнения.
- `Handler` - обработчик запроса
- `Server` - принимает запрос от коннектора, выбирает нить и обрабатывает с помощью handler-а

---
## К изучению
- [ ] https://www.eclipse.org/jetty/documentation/current/introduction.html#what-is-jetty