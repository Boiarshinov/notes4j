---
title: "Пагинация и Сортировка - Pageable"
tags:
  - spring
  - java_and_db
draft: false
---

Пагинация - это разделение выборки на страницы с указанным количеством элементов. Пагинация используется в тех случаях, когда количество запрашиваемых элементов слишком велико, а их единовременный запрос может привести к большим нагрузкам на сеть / базу.

В основе пагинации лежит разделение всего множества элементов на отдельные группы, называемые страницами. На каждой странице находится строго определенное количество элементов.

Интерфейс, демонстрирующий такую идею, используется во многих интернет-магазинах.

Пагинация в Spring поддерживается как на уровне контроллеров, так и на уровне JPA-репозиториев.

Основным интерфейсом для работы с пагинацией является `Pageable`.

### Параметры пагинации

У этого интерфейса есть три основных взаимосвязанных параметра (доступные через геттеры):
- `page` - номер страницы
- `size` - количество элементов в одной странице
- `offset` - отступ с начала выборки

Для однозначного задания параметров пагинации достаточно двух из них: либо `page` и `size`, либо `offset` и `size`.

В Spring по умолчанию используется первая пара параметров. Для того чтобы пользоваться второй парой, придется написать свою собственную реализацию `Pageable` (пример реализации смотри ниже).

---
## Сортировка

Для задания параметров сортировки результирующей выборки используется класс `Sort` и его подклассы.

Класс `Sort` может содержать в себе несколько параметров сортировки с различным направлением сортировки по каждому параметру. Совокупность параметра и направления сортировки представлена классом `Order`.

Направление сортировки задается с помощью перечисления `Direction`, которое может иметь два значения `ASC` и `DESC` - по возрастанию и убыванию соответственно. По умолчанию все сортировки происходят по возрастанию.

Для создания объекта `Order` используются статические методы генерации. Все они принимают строку с названием параметра сортировки.

- `asc(String)` - задает сортировку по указанному параметру по возрастанию
- `desc(String)` - задает сортировку по указанному параметру по убыванию
- `by(String)` - задает сортировку по умолчанию по указанному параметру

Класс `Order` иммутабельный, и если хочется в нем что-то поменять, необходимо создавать новый экземпляр.

//Про обработку null-параметров

Задание параметров сортировки производится с помощью удобного DSL:
```java
Sort sort = Sort.by(Sort.Order.asc("firstname"))
    .and(Sort.Order.desc("surname"));
```

Порядок записи параметров сортировки определяет ту последовательность, в которой будет отсортирована результирующая выборка.

### Типизация

Большим недостатком класса `Sort` является то, что параметры сортировки передаются в него в виде строк.

Для того чтобы обеспечить типобезопасность можно пользоваться специализированными классами.

Например `TypedSort` создается на основе сущности, по которой будет проводиться выборка. Параметры сортировки указываются с помощью ссылок на геттеры:
```java
TypedSort<Student> studentSort = Sort.sort(Student.class);
Sort sort = studentSort.by(Student::getFirstname).ascending()
    .and(studentSort.by(Student::getAge()).descending());
```
Если в проекте используется QueryDSL, то параметры сортировки могут задаваться с помощью класса `QSort` и сгенерированных Q или S классов.
```java
Sort sort = QSort.by(QStudent.firstname.asc())
    .and(Qsort.by(QStudent.age.desc()));
```
Существует также специальный класс, позволяющий задавать параметры сортировки с помощью средств JPA - `JpaSort`.

---
## Пагинация через отступ

Для того чтобы пользоваться пагинацией через отступ необходимо написать свою собственную реализацию `Pageable`. Пример такой реализации приведен ниже:
```java
public class OffsetBasedPageRequest implements Pageable, Serializable {
    private static final long serialVersionUID = -25822477129613575L;
    private final Integer offset;
    private final Integer limit;
    private final Sort sort;

    private OffsetBasedPageRequest(final Integer offset, final Integer limit, final Sort sort) {
        this.limit = limit;
        this.offset = offset;
        this.sort = sort;
    }

    public OffsetBasedPageRequest(final Integer offset, final Integer limit) {
        if (offset < 0) {
            throw new IllegalArgumentException("Offset index must not be less than zero!");
        }
        if (limit < 1) {
            throw new IllegalArgumentException("Limit must not be less than one!");
        }
        this.limit = limit;
        this.offset = offset;
        this.sort = Sort.by(Sort.Order.desc("id"));
    }

    @Override
    public int getPageNumber() {
        return offset / limit;
    }

    @Override
    public int getPageSize() {
        return this.limit;
    }

    @Override
    public long getOffset() {
        return this.offset;
    }

    @Override
    public Sort getSort() {
        return this.sort;
    }

    @Override
    public Pageable next() {
        return new OffsetBasedPageRequest(this.offset + this.limit, getPageSize(), getSort());
    }

    @Override
    public Pageable previousOrFirst() {
        return hasPrevious()
            ? new OffsetBasedPageRequest(this.offset - this.limit, getPageSize(), getSort())
            : first();
    }

    @Override
    public Pageable first() {
        return new OffsetBasedPageRequest(0, getPageSize(), getSort());
    }

    @Override
    public boolean hasPrevious() {
        return this.offset > this.limit;
    }
}
```

---
## К изучению
- [ ] [Гайд по пагинации](https://www.baeldung.com/spring-data-jpa-pagination-sorting) на Baeldung
- [ ] Javadoc на классы пагинации
- [X] [Реализация пагинации через отступ и размер страницы](https://stackoverflow.com/questions/25008472/pagination-in-spring-data-jpa-limit-and-offset)
- [ ] [Официальная документация](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters)