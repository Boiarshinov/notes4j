---
title: "Idempotent Consumer"
tags: 
  - architecture
  - pattern
draft: false
---

# Idempotent Consumer

__Idempotent Consumer__ - это архитектурный паттерн, описывающий способ _дедупликации_ сообщений, полученных из message broker'а.

При записи в message broker могут возникнуть различные сбои, которые могут привести к тому, что в брокере окажутся дубликаты одного сообщения.
Подписчики должны уметь работать с дубликатами и не обрабатывать их дважды.
Для этих целей используется паттерн идемпотентного консьюмера.

Принцип работы таков: каждое сообщение, опубликованное в брокере должно иметь уникальный ключ идемпотентности.
У каждого консьюмера должно быть хранилище, в котором он будет хранить обработанные ключи.
Когда консьюмер вычитывает событие из брокера, он должен проверить, не было ли уже обработано событие с таким ключом.
Если было, то событие пропускается.
Если нет, то консьюмер обрабатывает его и сохраняет результаты обработки в БД.
В той же транзакции консьюмер должен сохранить ключ в хранилище обработанных ключей.


Хороший вопрос - как долго хранить ключ сообщения?
В течение какого времени стоит ожидать дубликат, и когда он уже точно не придет?

---
## К изучению
- [ ] [Idempotent consumer](https://microservices.io/patterns/communication-style/idempotent-consumer.html)
- [ ] [EIP. Idempotent receiver](https://www.enterpriseintegrationpatterns.com/patterns/messaging/IdempotentReceiver.html)