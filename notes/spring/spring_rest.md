---
title: "Spring REST"
tags:
  - spring
  - web
draft: false
---

Отдельной библиотеки Spring REST не существует, но Spring предоставляет некоторые фичи, позволяющие упростить процесс создания RESTful сервисов.

Spring REST тесно интегрирован с библиотекой [Jackson](../external_lib/jackson.md) для конвертации приходящих json-строк в POJO и наоборот.

<mark>//Написать про то как можно выбрать тип данных JSON/XML и как работает автоматическая конвертация</mark>

<mark>//Написать про ResponseEntity</mark>

<mark>//Написать про MediaType</mark>

---
## Описание REST-контроллеров

RESTful контроллеры помечаются с помощью аннотации `@RestController`. Она является метааннотацией и включает в себя аннотацию `@Controller` над классом контроллера и аннотации `@ResponseBody` над возвращаемыми значениями всех публичных методов этого класса.

Совместно с `@RestController` зачастую используют аннотацию `@RequestMapping`, которая позволяет задать базовый URL для всех эндпойнтов контроллера.

Над публичными методами REST-контроллера, являющимися точками входа приложения, ставятся аннотации, соответствующие HTTP-методу:

- `@GetMapping` - для метода GET
- `@PostMapping` - для метода POST
- `@PutMapping` - для метода PUT
- `@DeleteMapping` - для метода DELETE
- `@PatchMapping` - для метода PATCH

Каждая из этих аннотаций может принимать строку, расширяющую URL до ресурса.

Также вместо этих аннотаций можно пользоваться более общей `@RequestMapping`, указывая у нее внутри тип HTTP-метода, но это не так наглядно.
```java
@RestController
@RequestMapping("/maintain")
public class MaintainController {
    @GetMapping("/info")
    public String info() { //полный URL ресурса - <host>/maintain/info
        return "fine";
    }
}
```

---
## Параметры запросов

Данные могут приходить в ресурс различными способами:

- в параметрах URL
- в пути URL
- в теле запроса

### Параметр URL

Для получения параметров URL можно использовать аннотацию `@RequestParam`, которая ставится перед аргументом метода, и самостоятельно извлекает параметр с нужным именем и присваивает его аргументу.
```java
@RequestMapping("/info")
public String getInfo(@RequestParam("version") String apiVersion) {
    //...
}
//Необязательно ставить аннотацию
```

### Параметр пути URL

Для получения данных из пути URL можно использовать аннотацию `@PathVariable(String)`, которая ставится перед аргументом метода и встраивается в путь в фигурных кавычках
```java
@RequestMapping("/task/{id}")
public String getTask(@PathVariable("id") Integer id) {
    //...
}
```

### Тело запроса

<mark>//написать</mark>

---
## Валидация параметров

<mark>//Написать про Validated и связь с Bean Validation</mark>

---
## К изучению:

- [X] Курс Spring & Hibernate for Beginners на Udemy в части касающейся