---
title: "Spring WebClient"
tags:
  - spring
  - http
draft: false
---

# Spring WebClient

Spring WebClient - это одна из реализаций [HTTP](../network/http.md)-клиента. 
WebClient тесно интегрирован с реактивным фреймворком webFlux.

WebClient сам не имеет интерфейсов для управления http-соединениями.
Вместо этого он обертывает другие HTTP-клиенты, предоставляя удобный API.
WebClient интегрирован с:
- Reactor Netty - используется по умолчанию
- JDK HttpClient, добавленный в [java 11](../java/java_versions.md)
- Apache HttpComponents


## Создание клиента
По умолчанию в контексте Spring не создается экземпляра WebClient, его нужно создать и сконфигурировать самостоятельно
```java
@Configuration
class WebClientConfig {

    @Bean("simpleWebClient")
    public WebClient simpleWebClient() {
        return WebClient.create("http://localhost:1234");
    }

    @Bean("customWebClient")
    public WebClient simpleWebClient() {
        return WebClient.builder()
            .baseUrl("http://localhost:1234")
            .build();
    }
}
```


---
## К изучению:

- [ ] [Краткая вводная. Статья на Baeldung](https://www.baeldung.com/spring-5-webclient)
