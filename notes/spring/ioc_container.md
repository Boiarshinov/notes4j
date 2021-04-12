---
title: "IoC-контейнер"
tags:
  - spring
draft: false
---

**IoC** - Inversion of Control - инверсия управления.

**IoC-контейнер** - система, которая создает объекты и управляет ими, а также внедряет зависимости одних компонентов от других.

В Spring IoC-контейнер называется **Spring-контейнер** или **ApplicationContext**.

---
## IoC

IoC - паттерн ООП, способствующий уменьшению связности между объектами.
Методы реализации:

- Внедрение зависимости
    - Через конструктор
    - Через сеттер (метод может иметь любое имя, необязательно начинаться с set
    - Через поле - не рекомендуется, т.к. используется рефлексия, замедляющая запуск приложения.
- Шаблон "Фабрика"

---
## Конфигурация IoC-контейнера

- конфигурация с помощью xml-файлов (старый способ, который встречается в legacy проектах)
- конфигурация на основе аннотаций и xml-файла (современный способ)
- конфигурация на основе Java-кода и аннотаций (современный способ)

### Конфигурация с помощью xml

Конфигурация IoC-контейнера может происходить с помощью xml-файла
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

      xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="trackCoach"
          class="dev.boiarshinov.springdemo.TrackCoach">
    </bean>
</beans>
```

Подробнее о том какие аттрибуты могут быть у бина в xml-файле и какие свойства можно определять внутри тега `<bean>`, можно прочитать в заметке про [бины](beans.md).

Для загрузки контекста из xml-файла используется класс `ClassPathXmlApplicationContext`:
```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
```

Бины, сконфигурированные с помощью xml-файла, можно доставать из контекста с помощью метода `getBean()`:
```java
Component component = context.getBean("componentImpl", Component.class);
```
- `Object getBean(String)` - возвращает бин по его идентификатору в xml-файле. Кастить к нужному типу придется самому.
- `T getBean(String, T.class)` - возвращает бин по его идентификатору в xml-файле и классу. При ошибке каста к указанному типу бросает `BeanNotOfRequiredTypeException`.
- `T getBean(T.class)` - возвращает бин указанного типа из xml- файла. Кидает исключение `NoUniqueBeanDefinitionException`, если бинов с подходящим классом несколько.

### Конфигурация на основе аннотаций и xml-файла

В xml-файл прописывается сканер, который будет искать аннотации, создающие бины в указанном пакете.
```xml
<beans ...>
    <context:component-scan base-package="dev.boiarshinov" />
</beans>
```

Основная аннотация для определения бина в java-коде - это аннотация `@Component`. Остальные аннотации для бинов описаны в заметке про [бины](beans.md).

Если бин имеет в зависимости другой бин, то для того чтобы IoC-контейнер создал зависимый бин и передал его экземпляр основному бину необходимо пометить поле (конструктор или сеттер) с помощью аннотации `@Autowired`. Если у бина всего один конструктор и в него передается все зависимые бины, то аннотацию можно даже не ставить (но лучше поставить).
```java
@Component
class MyComponentImpl implements IMyComponent {

    private Dependency1 dependency1;
    private Dependency2 dependency2;

    @Autowired
    public MyComponentImpl(Dependency1 dependency1, Dependency2 dependency2) {
        this.dependency1 = dependency1;
        this.dependency2 = dependency2;
    }
}
```

Если у интерфейса, который мы пытаемся autowire'ить имеется несколько реализаций, возникнет ошибка `NoUniqueBeanDefinitionException`. Для того чтобы указать IoC-контейнеру какую из реализаций, необходимо использовать, существует аннотация `@Qualifier(String name)`.

### Конфигурация на основе Java-кода и аннотаций

Для конфигурации IoC-контейнера с помощью Java-кода необходимо создать конфигурационный класс и обозначить его аннотацией @Configuration.

Создание бинов может производиться двумя способами:

- С помощью автоматического сканирования аннотацией `@ComponentScan(String packageName)` с указанием директории, в которой необходимо искать бины.
```java
@Configuration
@ComponentScan("dev.boiarshinov")
public class AppConfig {
    //...
}
```

- C помощью ручного создания бинов при помощи аннотации `@Bean`. При этом зависимости передаются с помощью методов создания бинов:

```java
@Configuration
public class AppConfig {
    @Bean
    public Component component() {
        return new Component();
    }

    @Bean
    public Service service() {
        return new Service(this.component());
    }
}
```

Название метода, над которым стоит аннотация @Bean, является идентификатором бина, по которому его можно получать из контекста.

В главном классе приложения загрузка контекста происходит с помощью класса `AnnotationConfigApplicationContext`, которому сообщается конфигурационный класс:
```java
public class MyApp {
    public static void main(String[] args) {

        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        //...
    }
}
```

---
## Циклические зависимости бинов

Если два бина зависят друг от друга, либо большое количество бинов образуют замыкающуюся цепочку зависимости, то такая зависимость называется циклической.

Циклическая зависимость является признаком плохой архитектуры проекта, и от нее необходимо избавляться.

---
## К изучению

- [X] IoC вики: https://ru.wikipedia.org/wiki/%D0%98%D0%BD%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D1%8F_%D1%83%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F
- [ ] Гайд по IoC на русском https://spring-projects.ru/guides/lessons/lesson-2/
- [X] Что такое IoC:  https://www.javaworld.com/article/2071914/excellent-explanation-of-dependency-injection--inversion-of-control-.html
- [ ] Мартин Фаулер о Dependency Injection: https://martinfowler.com/articles/injection.html