---
title: "Spring Async"
tags:
  - spring
  - multithreading
draft: false
---

# Spring Async

Spring Framework облегчает вынесение выполнения задач в отдельные [потоки](../java/multithreading/threads.md).
Для этого над методом, который должен исполняться асинхронно, ставится аннотация `@Async`.
К методу предъявляется ряд ограничений:
- над конфигурационным классом должна быть поставлена аннотация `@EnableAsync`;
- он должен быть публичным и объявлен в классе, который управляется спринговым контекстом;
- вызывать метод можно только извне класса, т.к. иначе чуда не произойдет, и метод будет вызван синхронно. Это связано с тем, что асинхронность добавляется в динамическом прокси, а если вызывать метод из того же класса, то он вызывается напрямую;
- метод должен возвращать один из 3 типов:
    - void
    - `Future`
    - `CompletableFuture`

В Spring Boot асинхронные таски по умолчанию выполняются в [пуле потоков](../java/multithreading/thread_pools.md) со следующими характеристиками:
- количество потоков: 8
- максимальный размер очереди: `Integer.MAX_VALUE`
- максимальное количество потоков: `Integer.MAX_VALUE`
- время, после которого бездействующих поток уничтожается - 60 с

Из-за таких настроек при большом количестве одновременно выполняемых асинхронных задач количество потоков может сильно возрастать, что будет сказываться на потребляемой памяти приложения, и может привести к OutOfMemory.

Переопределить стандартный пул потоков можно с помощью создания конфигурационного класса, наследующегося от `AsyncConfigurer`.
```java
@Configuration
public class AsyncExecutorConfig implements AsyncConfigurer {

  @Override
  public Executor getAsyncExecutor() {
    ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
    taskExecutor.setCorePoolSize(4);
    taskExecutor.setMaxPoolSize(32);
    taskExecutor.setQueueCapacity(50);
    taskExecutor.initialize();
    return taskExecutor;
  }
}
```

Или можно создать свой бин типа `TaskExecutor` и явно его указывать в аннотации `@Async`.
```java
@Bean(name = "myTaskExecutor")
public TaskExecutor myTaskExecutor() {
  ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
  executor.setCorePoolSize(4);
  executor.setMaxPoolSize(32);
  executor.setQueueCapacity(50);
  executor.setThreadNamePrefix("MyAppExecutor::");
  executor.initialize();
  return executor;
}

@Async("myTaskExecutor")
void doAsync() { /*...*/ }
```


---
## К изучению
- [X] [Как использовать `@Async`](https://www.linkedin.com/pulse/asynchronous-calls-spring-boot-using-async-annotation-omar-ismail/)