---
title: "Spring Security"
tags:
  - spring
  - web
  - security
draft: false
---

**Spring Security** - один из фреймворков, входящих в состав [Spring](spring.md).
Spring Security предназначен для определения прав пользователей на доступ к различным ресурсам.
Spring Security основывается на технологии Servlet Filter.

Аутентификация - проверка логина и пароля пользователя.
Авторизация - проверка того, что пользователь обладает достаточными правами для доступа к ресурсу.

Аутентификация может осуществляться различными методами:
- С помощью средств HTTP
- С помощью базовой спринговой формы аутентификации
- С помощью сервера аутентификации

По умолчанию Spring Security предоставляет защиту от:
- [CSRF](../security/csrf.md)
- <mark>???</mark>

Также Spring Security по умолчанию генерирует форму для аутентификации и отсылает туда всех неаутентифицированных пользователей.

Дополнительно можно включать:
- CORS

При аутентификации пользователю, на основе его данных, создается токен аутентификации, который помещается во все HTTP-запросы пользователя в Cookies.

---
## Фильтры
Spring Security основывается на спецификации [Filters](../servlets/filters.md), входящей в состав [Java EE Servlets](../servlets/servlet.md).
Spring Security для HTTP-запросов встраивает свой бин в цепочку фильтров сервлетов. 
Таким образом, любой запрос прежде чем попасть к ресурсу проходит через цепочку фильтров и, в том числе, через спринговый фильтр `DelegatingFilterProxy`. 
Спринговый фильтр является мостиком между сервлетными фильтрами и спринговым контекстом, он знает о спринговых бинах и позволяет встроить их в цепочку фильтров.
`DelegatingFilterProxy` оборачивает спринговый бин `FilterChainProxy`, который позволяет настраивать цепочку фильтров из сприговых бинов.
Также `FilterChainProxy` занимается очисткой Security Context, чтобы не текла память, и защищает приложения от некоторых типов атак.
`FilterChainProxy` может быть настроен так, что в зависимости от реквизитов запроса он будет перенаправлять его в различные цепочки фильтров.
Каждая цепочка может быть сгенерирована по-своему.

![Spring Security Filters](../../images/src/spring_security_filters.drawio.svg)

Настройка фильтров проводится с помощью SecurityFilterChain API:
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(Customizer.withDefaults())
            .authorizeHttpRequests(authorize -> authorize
                .anyRequest().authenticated()
            )
            .httpBasic(Customizer.withDefaults())
            .formLogin(Customizer.withDefaults());
        return http.build();
    }
}
```

Стандартные фильтры:
- `BasicAuthenticationFilter` - вытаскивает из запроса хэдер вида `Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`, парсит из него имя пользователя и пароль. Пытается аутентифицировать пользователя с помощью `AuthenticationManager` и при успехе записывает данные о пользователе в `SecurityContext`.
- `UsernamePasswordAuthenticationFilter` - используется в форме логина. Вытаскивает из параметров запроса username и password и пытается аутентифицировать пользователя с помощью `AuthenticationManager`. При успехе записывает данные о пользователе в `SecurityContext` и редиректит пользователя на ту страничку, с которой он попал на форму логина.


---
## Аутентификация
Аутентификация пользователей проводится объектом класса `AuthenticationManager`.
Он пропускает данные от пользователя через цепочку реализаций интерфейса `AuthenticationProvider`.
Этот интерфейс имеет не очень удачный API.
Его основной метод `Authentication authenticate(Authentication)` принимает и отдает объект одного и того же типа, что может сбивать с толку.
На самом деле в аргументе будут переданы креды пользователя, а на выходе нужно вернуть информацию о пользователе - его `UserDetails` (см. далее).

`Authentication` - это тоже интерфейс, предоставляющий базовую информацию о попытке аутентификации.
После успешной аутентификации объект `Authentication` укладывается в Security Context.
Самая базовая реализация этого интерфейса - `UsernamePasswordAuthenticationToken`.

`AuthenticationProvider` имеет ряд популярных реализаций:
- `DaoAuthenticationProvider` - достает данные о пользователе из `UserDetailsService`

---
## Конфигурация

Конфигурирование SpringSecurity производится в бине, который должен наследоваться от `WebSecurityConfigurerAdapter` (уже не актуально, класс был удален в Spring 6). Над классом в дополнение к стандартной `@Configuration` должна стоять аннотация `@EnableWebSecurity`.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter { /**/ }
```

Для дальнейшей конфигурации необходимо переопределить один из методов:
- `configure(HttpSecurity)` - позволяет:
    - сконфигурировать какие ресурсы требуют аутентификации, а какие нет.
    - включить или отключить защиты от CSRF, HTTP-атак и пр.
    - настроить форму аутентификации или отключить ее вовсе.
- `configure(WebSecurity)` -
- `configure(AuthenticationManagerBuilder)` - конфигурация пользователей и их ролей, хранения данных о пользователях

### Применимость аутентификации

Не все ресурсы приложения должны быть защищены. Некоторые из них могут быть общедоступными и не требовать аутентификации.

Настройка аутентификации проводится в методе `configure(HttpSecurity)` с помощью богатого DSL.

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests()
            .antMatchers("/secure/**").authenticated() //для доступа требуется аутентификация
            .antMatchers("/insecure/**").permitAll(); //получить доступ может кто-угодно
}
```

Для описания каких-либо URL, к которым должна быть применена особая настройка, используются [Шаблоны AntPathMatcher](ant_path_matcher.md).

---
## Данные о пользователях

Базовое представление пользователя находится в интерфейсе `UserDetails`.
Его основные методы:
- `String getUsername()` - имя пользователя
- `String getPassword()` - пароль
- `Collection<> getAuthorities()` - список прав пользователя

Реализации этого интерфейса различаются в зависимости от типа аутентификации.
Базовая имплементация - это класс `User`, но для LDAP это уже будет `Person`.

Права пользователя используются для того, чтобы проверить: имеет ли он право выполнять то или иное действие.
Для представления права используется интерфейс `GrantedAuthority` с простейшей реализацией `SimpleGrantedAuthority`.
Но для каких-то кастомных целях можно использовать другие реализации или написать собственную.

Для функционирования приложения необходимо установить какое-то хранилище данных о пользователях, где будут содержаться данные о логине и пароле пользователя и его ролях.
Эти данные могут храниться:
- в памяти - решение для hello world
- в базе данных
- во внешнем сервисе аутентификации
- на LDAP-сервере

Хранением и управлением пользователей занимаются интерфейсы `UserDetailsService` и `UserDetailsManager`.
`UserDetailsService` позволяет только получать информацию о пользователе, а `UserDetailsManager` позволяет также создавать новых пользователей или редактировать существующих.
Чтобы настроить хранение информации о пользователях нужно добавить в контекст бин одного из этих интерфейсов.
```java
@Bean
fun userDetailsManager(): UserDetailsManager {
    return InMemoryUserDetailsManager(
        User.builder()
            .username("user")
            .password("topsecret").build(),
        User.builder()
            .username("admin")
            .password("admin").build()
    )
}
```


### Роли

Роль является признаком пользователя, устанавливающим ему доступ к определенной группе ресурсов.

У каждого пользователя может быть несколько ролей.
<mark>//дальше</mark>



---
### Форма аутентификации

По умолчанию Spring Security предоставляет форму аутентификации. Если метод `configure(HttpSecurity)` переопределен, то форму аутентификации необходимо задать явно:

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .authorizeRequests() /* configuring */
        .and()
        .formLogin()
          .permitAll()
        .and()
        .logout().permitAll();
}
```

При попытке получения защищенного ресурса неавторизованным пользователем, он будет перенаправлен на страницу `/login`.

По умолчанию при аутентификации имя пользователя и пароль передаются в теле POST запроса.

Для выхода из аккаунта необходимо перейти по адресу `/logout`.

При выходе из аккаунта затирается сессия пользователя и куки, относящиеся к ней.

При разделении веб-приложения на фронтенд и бекенд в части бекенда не должно быть эндпойнта для аутентификации, поэтому его нужно явно отключить (если метод `configure(HttpSecurity)` и так переопределен, то не нужно):

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .formLogin().disable();
}
```

### Ошибка авторизации

При ошибке авторизации при недостаточном наборе прав для доступа к какому-либо ресурсу с сервера возвращается ошибка 403 Forbidden.

Для того чтобы перенаправить пользователя по какому-либо URL при ошибке авторизации в конфигурационном методе `configure(HttpSecurity)` необходимо задать определенные настройки c помощью метода `exceptionHandling()`:

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .exceptionHandling().accessDeniedPage("/access-denied");
}
```

### Защита от CSRF

Защита от [CSRF](../security/csrf.md) происходит с помощью токена.

Проверка токена проводится в одном из фильтров Spring Security. При отсутствии в запросе заголовка с токеном, будет выброшена ошибка 403.

Для защиты во все Spring MVC формы будет автоматически вставлен следующий html-код. Значение при этом генерируется сервером для каждого запроса любой страницы.

```html
<input name="_csrf" type="hidden" value="3f60f92e-512a-49e5-8c54-97d0d1c60f28" />
```

Данная защита включена по умолчанию. Но при использовании веб-приложения как бекенда защита от CSRF не нужна (скорее всего) и ее можно отключить в методе `configure(HttpSecurity)`

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        .csrf().disable();
}
```

---
## Доступ к методу

С помощью аннотаций Spring Security можно ограничить доступ к методу для определенных ролей или исходя из выполнения каких-либо условий.

Важные моменты:

- Если одно и то же условие указывается над каждым методом в классе, то можно вынести эту аннотацию над классом.
- Для одного метода можно указать несколько аннотаций доступа

### Аннотации `@Secured` и `@RoleAllowed`

Аннотации являются аналогами друг друга, ставятся над методом, в скобках указываются роли, которым разрешен доступ к методу:
```java
@Secured("MODERATOR", "REGULAR_USER")
public String getSecretWord() {
    //...
}
```

### Аннотации `@PreAuthorize` и `@PostAuthorize`

Эти аннотации принимают предикаты, которые в свою очередь указывают на то, имеет ли пользователь доступ к методу.

Указание на предикат (метод, возвращающий boolean) задается с помощью SpEL, и может вести к методу утилитного класса
```java
@PreAuthorize("@judgeClass.hasAccess()")
public String getSecretWord() {
    //...
}
```

Аннотация `@PreAuthorize` осуществляет проверку до входа в метод. А аннотация `@PostAuthorize` - после выполнения метода, и может изменить результат.

Благодаря чему можно получить доступ к результату выполнения метода и сравнить его с чем-либо, получив предикат.

```java
@PreAuthorize("returnObject.userName == authentication.principal.nickName")
public CustomUser getUserInfo(Integer userId) {
    //...
}
```

---
## Хранение данных о пользователях

Spring Security из коробки позволяет хранить данные о пользователях в базе данных. Для этого в БД должны быть созданы две таблицы `users` и `authorities`, связанные между собой один ко многим.

В таблице `users` хранятся данные о пользователях, а в таблице `authorities` данные о том какому пользователю соответствуют какие роли.

Пароли хранятся в таблице `users` в специальном формате:
`{id}encodedPassword`
где id это способ шифрования пароля. Существуют следующие способы:

- `noop` - без шифрования
- `bcrypt` - одностороннее хэширование пароля (рекомендованное разработчиками Spring Security)
- другие

---
## Подключение библиотеки

В Spring Boot приложениях для подключения Spring Security достаточно добавить в pom.xml одну зависимость:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

---
## К изучению:

- [ ] [Про доступ к методам](https://www.baeldung.com/spring-security-method-security)
- [ ] [Официальная документация](https://docs.spring.io/spring-security/site/docs/5.4.1/reference/html5/#introduction) на Spring Security
- [ ] [Архитектура](https://docs.spring.io/spring-security/reference/servlet/architecture.html) Spring Security
- [ ] [Краткий обзор](https://habr.com/ru/post/470786) Spring Security
- [X] Курс Spring & Hibernate for Beginners на Udemy в части касающейся
- [ ] [Способы аутентификации в REST-сервисах](https://habr.com/ru/post/245415)
- [X] [Сравнение видов шифрования паролей](https://medium.com/@danboterhoven/why-you-should-use-bcrypt-to-hash-passwords-af330100b861)