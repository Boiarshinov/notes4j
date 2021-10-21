---
title: "Spring Bean"
tags:
  - spring
draft: false
---

**Spring Bean** или просто бин (Bean) - это любой объект, который находится под управлением Spring ACDI.

Бины создаются с помощью конфигурационных метаданных, например с помощью `applicationContext.xml` в тегах `<bean>`:
```xml
<bean id="myBean"
      class="dev.boiarshinov.MyBean" >
    <constructor-arg value="Arty" />
</bean>
```

Определение бинов очень созвучно с [Java Bean](../java/java_bean.md), но в отличие от них Spring Beans не обязаны быть сериализуемыми, могут не иметь конструктора без параметров и т.д.

Для того чтобы воспользоваться Spring Beans, необходимо подключить три зависимости: `spring-core`, `spring-beans` и `spring-context`.

---
## Жизненный цикл бинов

Жизненный цикл бинов очень похож на жизненный цикл сервлетов.

- Создание объектов
- Внедрение зависимостей
- Вызов `init-method`
- Использование бина по назначению
- Вызов `destroy-method`

`init` и `destroy`-методы не должны иметь аргументов. Модификатор доступа и тип возвращаемого значения могут быть любыми.

---
## Области применения бинов

Область применения бинов определяется параметром scope, который может принимать 5 возможных значений:

- `singleton` - объект такого класса может быть только один в контейнере IoC. Данное значение принимается по умолчанию. Любой вызов getBean() из java-кода будет возвращать один и тот же объект.
- `prototype` - контейнер IoC будет создавать новый экземпляр на каждый полученный запрос getBean().
- Используемые в веб-приложениях:
    - `request` - IoC-контейнер создает бин для каждого http-запроса.
    - `session` - IoC-контейнер создает бин для каждой сессии.
    - `application` - то же, что и `singleton`, но в веб-приложениях, использующих общий ServletContext, будет создан только один такой бин на все приложения.
    - `web-socket` - IoC-контейнер создает бин для каждой сессии web-сокетов.

Область применения бинов задается либо в xml-файле с помощью параметра scope бина, либо с помощью аннотации @Scope над классом бина.

**Важно!** Spring не вызывает destroy-method на бинах с областью применения `prototype`. Если необходимо избавиться от prototype-бина, то это нужно сделать самому. Либо унаследовать свой бин от `DisposableBean` и реализовать класс, расширяющий `BeanPostProcessor` (подробнее см. тут: https://howtodoinjava.com/spring-core/spring-bean-post-processors/)

---
## Способы определения бинов:

- конфигурация с помощью `applicationContext.xml` (старый способ, который еще кое-где используется)
- конфигурация на основе аннотаций и xml (современный способ)
- конфигурация на основе аннотаций и Java-кода (современный способ)

### Определение бина в applicationContext.xml

У бина существуют следующие *атрибуты*:

- `id`/`name` - имя экземпляра бина.
- `class` - ссылка на Java-класс.
- `scope` - область видимости.
- `lazy-init` - если true, то экземпляр будет создан не при запуске приложения, а при первом запросе.
- `init-method` - определяется метод инициализации бина. Обычно тут открываются ресурсы. Метод в Java-классе может иметь любой модификатор доступа.
- `destroy-method` - определяется метод уничтожения бина. Обычно тут закрываются ресурсы. Если scope=prototype, то метод destroy не вызывается.
- `factory-method` - определяет фабричный метод, с помощью которого создается объект

И следующие элементы, которые определяются в своих тегах:

- `constructor-arg` - значения аргументов, передаваемых в конструктор для создания экземпляра бина. Если этот элемент не определяется, значит у класса должен быть пустой конструктор.

```xml
<bean id="trackCoach"
      class="dev.boiarshinov.springdemo.TrackCoach">
    <constructor-arg ref="myFortuneService" />
</bean>
```

- `property` - устанавливает значение полю класса с помощью сеттера (с помощью ref). Таким образом можно устанавливать значения не только зависимым компонентам, но и полям примитивных типов (с помощью value).

```xml
<bean id="cricketCoach"
      class="dev.boiarshinov.springdemo.CricketCoach">
    <property name="fortuneService" ref="myFortuneService" />
    <property name="emailAddress" value="yourCoach@gmail.com" />
</bean>
```

С помощью этих элементов можно внедрять зависимости (одни бины к другим бинам).

### Определение бина с помощью аннотаций

Для считывания аннотаций над классами спрингом необходимо прописать в `applicationContext.xml`:
```xml
<context:component-scan base-package="packageName" />
```

Либо определить специальный класс с аннотацией `@Configuration`
Аннотации:

- `@Component([String beanId])` - определяет класс, как Spring Bean и создает бин.
- `@Autowired` - подбирает подходящий бин по классу. Может использоваться на конструкторах, сеттерах и полях (даже приватных).
- `@Qualifier(String beanName)` - указывает какой из подходящих бинов необходимо внедрить
- `@Value(String)` - определяет значение параметра. Записывается над полем
- `@Scope(String scopeType)` - определяет область видимости. Записывается над названием класса.
- `@PostConstruct` - помечает метод, как инициализирующий
- `@PreDestroy` - помечает метод, как destroy-method

```java
@Component("musicPlayer")
@Scope("singleton")
public class MusicPlayer {

    private ClassicalMusic classicalMusic;
    private RockMusic rockMusic;
    private RapMusic rapMusic;

    @Value("${musicPlayer.volume}")
    private int volume;
    @Value("${musicPlayer.darkTheme}")
    private boolean darkTheme;

    @Autowired
    public MusicPlayer(@Qualifier("rapMusic") RapMusic rapMusic,

                      @Qualifier("classicalMusic") ClassicalMusic classicalMusic,

                      @Qualifier("rockMusic") RockMusic rockMusic) {
        this.rapMusic = rapMusic;
        this.classicalMusic = classicalMusic;
        this.rockMusic = rockMusic;
    }

    @PostConstruct
    private void init(){
        System.out.println("Initiating Music Player");
    }

    @PreDestroy
    private void destroy(){
        System.out.println("Destroying Music Player");
    }
}
```

Если в аннотации `@Component` не указывать beanId, то Spring создаст его самостоятельно по имени класса (сделав первую букву строчной).

### Разновидности компонентов

В Spring существуют предустановленные виды компонентов:

- `@Controller` - компонент-контроллер, предназначенный для инкапсуляции логики приема запроса и конвертации пришедших данных в сущности, используемые в приложении. Благодаря этой аннотации Spring ищет в классе, помеченным ей, точки входа в приложение по аннотациям `@RequestMapping` (и ее подвидам)
- `@Service` - компонент, предназначенный для написания в нем бизнес-логики приложения. Spring не добавляет подобным компонентам каких-либо дополнительных возможностей.
- `@Repository` - предназначен для DAO слоя. Благодаря этой аннотации Spring будет оборачиваться возможные исключения JDBC в более понятные.

Каждая из этих аннотаций содержит в себе аннотацию `@Component` внутри.

---
## Создание бина

Бин может быть создан одним из следующих способов:

- с помощью конструктора
- с помощью пустого конструктора и сеттеров
    - хардкодинг в xml
    - считывание с файла .properties
- с помощью того и другого одновременно
- с помощью фабричного метода

**Конструктор** может принимать либо строковые аргументы, либо другие бины, что производится с помощью следующих атрибутов:

- `value` -
- `ref` - передает ссылку на другой бин. Здесь нужно указывать id другого бина.
- `name` -
- `index` -
- `type` -

```xml
<bean id="musicPlayerBean"
      class="dev.boiarshinov.MusicPlayer">
    <constructor-arg ref="musicBean" />
</bean>
```

**Параметры** задаются с помощью атрибутов:

- `name` - имя параметра, должно соответствовать наименованию сеттера. Например, параметр firstName - сеттер setFirstName()
- `value` - строковое значение (может быть преобразовано к любому примитиву).
- `ref` - значение в виде другого бина.

Параметры также можно инициализировать списками, картами, множествами и массивами.

---
## К изучению

- [ ] https://proselyte.net/tutorials/spring-tutorial-full-version/introduction-into-beans/
- [X] [Разница между Spring Bean, JavaBean и POJO](https://www.shaunabram.com/beans-vs-pojos/)
- [X] [Ролики от Алишева](https://www.youtube.com/playlist?list=PLAma_mKffTOR5o0WNHnY0mTjKxnCgSXrZ)
- [ ] [Гайд](https://www.baeldung.com/spring-bean-scopes) по областям применения бинов на Baeldung
- [ ] [Проблема сужения области видимости бинов](https://www.logicbig.com/tutorials/spring-framework/spring-core/injecting-singleton-with-prototype-bean.html#narrow-scoped-bean-sol) при внедрении
- [X] [Отличия в разновидностях аннотации `@Component`](https://stackoverflow.com/questions/6827752/whats-the-difference-between-component-repository-service-annotations-in)