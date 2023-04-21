---
title: "CompletableFuture"
tags:
  - multithreading
draft: false
---

# CompletableFuture

По умолчанию CompletableFuture использует дефолтный пул потоков `ForkJoinPool.commonPool()` для выполнения задач.
Но его всегда можно переопределить.

Если в ходе исполнения CompletableFuture на одном из этапов вылетит исключение, то последующие этапы пропускаются.


## Методы

### Создание
- `new CompletableFuture()` - создает цепочку, но пока не начинает ее исполнение
- `supplyAsync(Supplier)` - сразу запускает
- `runAsync(Runnable)` - сразу запускает
- `completedFuture(T)` - возвращает сразу исполненную футуру. Часто используется в тестах для мокирования методов, возвращающих `CompletableFuture`.

### Промежуточные операции
- `thenApply(Function<T, R>)` - преобразует результат исполнения футуры. Аналог `map()` из стримов. Действие выполняется в том же потоке, который исполнял предыдущее действие
- `thenApplyAsync(Function<T, R>)` - то же самое, но действие выполняется в другом потоке
- `thenCompose(Function<T, CompletableFuture<R>>)` - похож на `thenApply`, используется в тех случаях, когда внутри функции возвращается `CompletableFuture`. Метод объединяет ее с текущей. По сути это аналог `flatMap() из стримов.

- `thenAccept(Consumer)` - консьюмит результат исполнения футуры в какой-то сайд-эффект. Аналог `consume()`
- `thenRun(Runnable)` - выполняет код после исполнения футуры

### Конечные операции
- `get()` - запускает цепочку вызовов, если она еще не была запущена и синхронно дожидается результата. Выбрасывает несколько проверяемых исключений
- `join()` - дожидается выполнения цепочки и возвращает результат. При ошибках, выбрасывает непроверяемое исключение
- `getNow(T)` - вернет либо результат, либо переданное значение по умолчанию, если результат не готов
- `complete()`
- `cancel()`

### Обработка исключений
Обычно эти методы добавляются в самом конце цепочки колбеков.
- `handle()` - позволяет обработать либо результат, либо исключение, а-ля монада Try
- `exceptionally()` - поток исполнения зайдет сюда, если ранее по цепочке вылетело исключение. Если исключения не было, то метод будет пропущен

### Обработка таймаутов
- `completeOnTimeout(E, long, TimeUnit)` - если задача не успела выполниться за заданное время, то она прерывается, а в результате возвращается значение из первого аргумента.

### Объединение футур
Логическое И:
- `runAfterBoth()` - выполнится, когда оба этапа завершились
- `thenCombine()` - объединяет результат и передает значение дальше
- `allOf(CompletableFuture...)` - завершается, когда все этапы выполнятся. Если в одной из футур вылетит исключение, то и `allOf()` упадет с исключением. Поэтому, если нужно корректно обработать возможные исключения, то следует в каждой `CompletableFuture` прописать собственную обработку исключений

Логическое ИЛИ:
- `runAfterEither()`
- `acceptEither()`
- `applyToEither()`
- `anyOf()` - завершается, когда выполнится любой из этапов


## Конкретные кейсы
Сюда бы добавить примеры того, как с помощью футур решать часто возникающие задачи.
Например, сделать асинхронные запросы к двум сервисам и объединить их результаты.

```java
List<CompletableFuture<Result>> refreshingTasks = addresses.stream()
                .map(a -> {
                    String address = isLocalAdmin(a) ? "local" : a;
                    Others.MountTableManager manager = managerFactory.apply(address);
                    return CompletableFuture.supplyAsync(() -> new Result(a, manager.refresh()), executor)
                            .completeOnTimeout(new Result(a, false), cacheUpdateTimeout, TimeUnit.MILLISECONDS)
                            .exceptionally(e -> new Result(a, false));
                })
                .collect(Collectors.toList());

        CompletableFuture.allOf(refreshingTasks.toArray(new CompletableFuture[0])).thenAccept(_void ->{
            List<Result> results = refreshingTasks.stream()
                    .map(CompletableFuture::join)
                    .collect(Collectors.toList());

            results.stream()
                    .filter(result -> !result.success)
                    .forEach(result -> removeFromCache(result.address));

            logResults(results);
        }).join();
```


---
## К изучению
- [ ] [Video CompletableFuture in Java 8](https://www.youtube.com/watch?v=-MBPQ7NIL_Y&ab_channel=JPoint%2CJoker%D0%B8JUGru)
- [ ] [Куксенко. Как сделать CompletablFuture еще быстрее](https://www.youtube.com/watch?v=W7iK74YA5NM&ab_channel=JUGNsk)
- [X] [Курс по многопоточности в Java](https://fillthegaps.getcourse.ru/mt7)