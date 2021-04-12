---
title: "Файлы"
tags:
  - io
draft: false
---

## Класс File
Решает задачу доступа к файловой системе
Можно указывать как файлы, так и папки:
```java
File file = new File("F:\\a.txt");
File directory = new File("F:\\XXX");
```

Объект `File` без проблем может указывать на несуществующий файл или директорию

Класс файл имеет много недостатков: например, многие методы возвращают тип boolean, говорящий об успешности выполненной операции, вместо того, чтобы быть void и бросать исключения при неудаче. Также нет методов для перемещения и копирования файлов.

Поля:
- `static final String separator` - константа, которая хранит в себе разделитель между папками (отличается для Windows и Unix)
- `static final char separatorChar` - то же самое, но в формате char
- `static final String pathSeparator` - константа, которая хранит в себе символ разделения путей (в Windows - ';')
- `static final char pathSeparatorChar` - то же самое, но в формате char

Конструкторы:
- `File(String sourse)` - нужно записать полный путь
- `File(String sourseDir, String fileName)` - отдельно записывается путь директории и отдельно файл

Методы:
- `String getPath()` - возвращает путь к файлу
- `String getName()` - возвращает имя файла вместе с расширением
- `String getParent()` - возвращает родительскую директорию
- `boolean isAbsolute()` - является ли путь, с которого мы читаем абсолютным (начинающимся из корня системы)
- `String getAbsolutePath()` - возвращает абсолютный путь к файлу
- `File getAbsoluteFile()` - возвращает файл
- `String getCanonicalPath()` - возвращает абсолютный путь в каноничном виде (используется для того чтобы сравнить два пути - не ведут ли они к одному и тому же файлу)
- `File getCanonicalFile()` - то же, но в виде файла
- `boolean exists()` - проверяет: существует ли указанный файл
- `boolean isFile()` - проверяет файл ли это
- `boolean isDirectory()` - проверяет директория ли это
- Для работы с файлами:
    - `int length()` - возвращает размер файла (или ноль, если файла не существует)
    - `long lastModified()` - время в мс до последнего редактирования от 1 января 1970 г. (или ноль, если файла не существует)
    - `boolean createNewFile()` - атомарно создает новый пустой файл, возвращает true, если все прошло успешно. Метод используется очень редко и в основном для проверки того, существует ли уже такой файл, в который мы хотим начать запись.
    - `static File createTempFile(String prefix, String suffix)` - Создает временный файл в текущей директории с указанным префиксом и суффиксом
    - `static File createTempFile(String prefix, String suffix, File path)` - Создает временный файл в указанной директории с указанным префиксом и суффиксом
    - `boolean delete()` - удаляет файл, возвращает успех.
    - `boolean renameTo(File)` - переименовывает файл, возвращает успех. Не рекомендуется при этом менять директорию расположения!
- Для работы с директориями:
    - `String[] list()` - получить содержимое директории (поддиректории не возвращает) в виде массива строк (или null, если директории не существует)
    - `String[] list(FileFilter)` - то же, что и предыдущей, но можно фильтровать файлы, имеющие определенное расширение
    - `File[] listFiles()` - получить содержимое директории в виде массива файлов (или null, если директории не существует)
    - `File[] listFiles(FileFilter)` - то же, что и предыдущей, но можно фильтровать файлы, имеющие определенное расширение
    - `boolean delete()` - удаляет директорию, возвращает успех. На момент удаления она должна быть пустой!
    - `boolean mkdir()` - создает одну директорию
    - `boolean mkdirs()` - создает директорию и все директории, которые должны быть перед ней
```java
File file = new File("a/b/c/d");
file.mkdirs(); //Создает все эти папочки
```

Тонкости при указании пути:
- `.` - родительская директория
- `..` - переход на директорию выше

## Интерфейсы FileFilter и FilenameFilter
Имеют единственный метод:
- `FileFilter` - `boolean accept(File )`
- `FilenameFilter` - `boolean accept(File dir, String name)`

<mark>А зачем нужны то?</mark>


# НОВОЕ API - NIO.FILE (также называемое NIO2 API)

## Интерфейс Path

В Java 7 разработчики, осознав ничтожность класса `File`, создали новый класс `Path`, находящийся в пакете `nio.file`.

Объект `Path`, так же как и объект `File`, не привязан к существованию файла на диске.

Интерфейс `Path` не имеет доступа к файловой системе. Для того чтобы его получить используется класс `Files`.

Методы:
- `File toFile()` - преобразует Path в File и возвращает его
- `boolean isAbsolute()` - является ли путь, с которого мы читаем абсолютным (начинающимся из корня системы)
- `String getFileName()` - возвращает имя файла
- `String getParent()` - возвращает родительскую директорию
- `int getNameCount()` - возвращает количество элементов в пути к файлу
- `Path getName(int index)` - Возвращает компонент пути по его индексу
- `Path resolveSibling(Path / String)` - меняет название файла на переданное
- `boolean startsWith(String)` - проверяет, является ли путь, переданный в аргументе, префиксом объекта
- `Path relativize(Path)` - вычисление относительного пути между двумя файлами
- `Path resolve(Path)` - присоединяет переданный путь к пути, вызвавшему метод.
```java
Path dir = Paths.get("F:/JavaTest");
Path file = Paths.get("a.txt");
System.out.println(dir.resolve(file).toString()); //F:/JavaTest/a.txt
```

- `String toString()` - возвращает полный путь (???) в виде строки
- `long copy(Path source, Path target)` - копирует файл из одного пути в другой, возвращает количество перезаписанных байт
- `long copy(Path source, OutputStream)` - копирует файл в поток вывода
- `long copy(InputStream, Path target)` - копирует файл из потока ввода
- `move()`

## Класс Paths
Класс `Paths` является фабрикой для создания новых объектов класса `Path`.

- `static get(String path, String...)` - возвращает объект типа Path с директорией, переданной в качестве аргумента
- `static get(URI)` -
```java
Path path = Paths.get("a/b/c/d");
```

## Класс Files

Класс Files является утилитным и предоставляет только статические методы для работы с файлами.

Методы:
- `static boolean exists(Path)` - возвращает существует ли такой файл
- `static boolean isRegularFile(Path)` - является ли указанный путь файлом
- `static long size(Path)` - возвращает размер файла в байтах
- `static FileTime getLastModifiedTime(Path)` - возвращает время последнего изменения
- `static copy(Path, Path, CopyOption...)` - копирует файл
- `static move(Path, Path, CopyOption...)` - перемещает файл
- `static newDirectoryStream(Path)` - возвращает объект класса `DirectoryStream<Path>` - создает стрим из всех файлов в директории. Обычно записывается в try-with-resourses
- `static void createDirectory(Path)` - создает директорию
- `static void createDirectories(Path)` - создает директорию и все директории, которые должны быть перед ней
- `static void walkFileTree(Path, FileVisitor<Path>)` - обходит дерево файлов, начиная с переданной директории
- `List<String> readAllLines(Path, StandartCharset)` - считывает с список все строки в файле (используется для маленьких файлов)
- `void write(Path, List<String>, StandartCharset)` - записывает в файл список строк в указанной кодировке
- `void createTempFile(Path dir, String prefix, String suffix)` - создает временный файл в указанной директории с указанным именем
- Создание потоков ввода-вывода:
    - `InputStream newInputStream(Path)` - открывает поток на чтение из файла
    - `BufferedReader newBufferedReader(Path)` - открывает буферизованный символьный поток на чтение из файла
    - `OutputStream newOutputStream(Path)` - открывает поток на запись в файл
    - `BufferedWriter newBufferedWriter(Path)` - открывает буферизованный символьный поток на запись в файл

## Класс `DirectoryStream<Path>`
Получить объект такого класса можно следующим образом:
```java
try (DirectoryStream<Path> dirStream = Files.newDirectoryStream(path)) {
} catch () {}
```

## `Класс SimpleFileVisitor<Path> impelements FileVizitor`
Класс обычно наследуют и переопределяют необходимые методы

Методы:
- `FileVisitResult visitFile(Path, BasicFileAttributes)` - в методе записываем набор действий, которые нужно выполнить во время посещения текущего файла
- `FileVisitResult postVisitDirectory(Path, IOException)` -
- `FileVisitResult visitFileFailed(Path, IOException)` - устанавливает обработку тех файлов, которые не удается вызвать
- `FileVisitResult preVisitDirectory(Path, BasicFileAttributes)`

При этом `FileVisitResult` - это перечисление, которое может принимать следующие значения:
- `CONTINUE` - продолжает обход по дереву файлов
- `TERMINATE` - прекращает обход по дереву файлов
- `SKIP_SUBTREE` - пропускает данную категорию и продолжает обходить дерево
- `SKIP_SIBLINGS` - пропускает все директории, которые лежат в той же папке, что и та, откуда был возвращен экземпляр перечисления

---
## К изучению
- [ ] Сравнение io и nio: https://docs.oracle.com/javase/tutorial/essential/io/legacy.html
- [X] Про walkFileTree(): https://habr.com/ru/post/437694/
- [X] Про Path, Paths, Files: https://javarush.ru/groups/posts/2275-files-path