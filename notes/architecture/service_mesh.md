---
title: "Service Mesh"
tags:
  - architecture
draft: false
---

# Service Mesh

Service Mesh - это архитектурный паттерн, позволяющий упростить интеграцию сервисов между собой за счет использования оркестратора и дополнительных микроконтейнеров или демонов, стоящих рядом с сервисами.
Бизнес приложение вместо того, чтобы самому заниматься дискаверингом, ретраями и прочим, просто отправляет исходящие запросы в дополнительный микроконтейнер Service Mesh, а тот уже маршрутизирует запрос куда нужно.

Service Mesh позволяет упростить канареечный деплой, внедрить A/B тестирование.
Также Service mesh берет на себя ретраи при вызове сторонних сервисов, занимается circuit breaker, отслеживает healthcheck.
Service Mesh может также брать на себя обязанность по сбору метрик, логов и трейсов.

Service Mesh делится на две части:
- __Control Plane__ - service discovery
- __Data Plane__ - это микроконтейнеры или демони, поднятные рядом с каждым сервисом

### Control Plane
Control Plane может быть как монолитным, так и микросервисным, где каждый сервис отвечают за свою часть функционала.
Монолитные проще сопровождать, а микросервисные более гибкие.

Control Plane предоставляет API для sidecar proxy. 
Единого стандартного API не существует, каждое решение предоставляет свой API.

### Data Plane
Варианты:
- sidecar-контейнер - для каждого сервиса отдельный дополнительный контейнер
- node daemon - отдельный демон на каждом хосте. На хосте при этом может быть развернуто несколько сервисов
- proxyless - редко встречающийся подход, хз как работает


---
## Решения:
- Istio - монолитный control plane, data plane на основе sidecar proxy. Самый популярный вариант
- Linkerd - микросервисный control plane, data plane на sidecar proxy
- Nginx
- Consul
- Traefic - микросервисный control plane, data plane на node daemon. Считается устаревшим
- Kuma
- Open Service Mesh


Недостатки Service Mesh:
- сложность управления инфраструктурой
- увеличение latency из-за прохода трафика через data plane
- кушает ресурсы
- усложнение отладки