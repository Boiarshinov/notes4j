---
title: "JMH"
tags:
  - performance
draft: false
---

# JMH

JMH - Java Microbenchmark Harness - фреймворк для измерения производительности небольших фрагментов кода. 
Библиотека разработана Алексеем Шипилёвым.

---
## Написание бенчмарок

todo

Результаты бенчмарок по умолчанию выводятся в консоль, но если очень хочется, то можно их прихранить в json (и не только) файлик:
```java
Options options = new OptionsBuilder()
    .include(CounterBenchmarks.class.getName())
    .forks(1)
    .resultFormat(ResultFormatType.JSON)
    .result("benchmark-result.json")
    .build();
```

---
## Подключение и запуск

Рекомендуется создавать отдельный проект для JMH тестов, чтобы окружающий код точно никак не влиял на бенчмарки.

### Подключение

Самый простой способ подключить JMH - создать подпроект с помощью архетипа Maven.
У меня так сделать не получилось, поэтому вот просто список необходимых зависимостей
```xml
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-core</artifactId>
    <version>${jmh.version}</version>
</dependency>
<dependency>
    <groupId>org.openjdk.jmh</groupId>
    <artifactId>jmh-generator-annprocess</artifactId>
    <version>${jmh.version}</version>
    <scope>provided</scope>
</dependency>
```

### Запуск

Запускать бенчмарки можно двумя способами: 
- с помощью maven плагина
- с помощью экземпляра класса `Runner` . При таком способе бенчмарки можно запускать прямо из IDE

Алексей Шипилёв утверждает, что второй способ запуска может быть менее точным, чем первый.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.0</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <finalName>benchmarks</finalName>
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                <mainClass>org.openjdk.jmh.Main</mainClass>
                            </transformer>
                        </transformers>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```


---
## К изучению:
- [ ] [Примеры использования библиотеки, одновременно являющиеся документацией](https://github.com/openjdk/jmh/tree/master/jmh-samples/src/main/java/org/openjdk/jmh/samples)
- [ ] [Шипилев. Java Benchmarking: как два таймстампа прочитать!](https://www.youtube.com/watch?v=8pMfUopQ9Es&ab_channel=JPoint%2CJoker%D0%B8JUGru). 57:28
- [X] [Web-тулза для визуализации результатов](https://jmh.morethan.io/)
