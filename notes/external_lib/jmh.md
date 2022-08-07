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
