---
title: "Spring Boot Actuator"
tags:
  - spring
  - web
draft: false
---

Spring Boot Actuator - это система мониторинга за состоянием приложения, которая предоставляет несколько конечных точек.

<mark>//Описать конкретные ручки</mark>

---

## Настройка

Настройка Spring Boot Actuator проводится в `application.properties` / `application.yml`:

```yml
management:
  endpoint:
    metrics:
      enabled: true
    prometheus:
      enabled: true
  endpoints:
    web:
      basePath: /maintain/inner
      exposure:
        include: '*'
  metrics:
    export:
      prometheus:
        enabled: true
```

---
## Подключение в Maven

В Spring Boot приложении необходимо вставить в pom.xml еще одну зависимость:
```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-actuator</artifactId>
  </dependency>
```

---
## Админка для Актуатора

Существует open-source библиотека, которая предоставляет красивый интерфейс для актуатора и помимо прочего позволяет просматривать логи приложения: https://github.com/codecentric/spring-boot-admin

---
## К изучению

- [X] Введение в Spring Boot Actuator: https://habr.com/ru/company/otus/blog/452624/
- [ ] Официальная документация: https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html
- [X] Курс Spring & Hibernate for Beginners на Udemy в части касающейся