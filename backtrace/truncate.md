# `truncate*()`-методы класса [Trace](./Trace.md)

Группа этих методов обрезает стек до указанной точки.
Обычно это точка входа в какое-то пространство имён или библиотеку.
Удобно при [работе с исключениями](ExceptionTrace.md)

## Пример

Например, мы используем стороннюю библиотеку для работы с базой данной.
Где-то в недрах нашего кода мы вызываем эту библиотеку:

```php
$db->query('SELECT * FROM `test` WHERE `id`=?i')->el();
```

Здесь мы совершили ошибку (не указали значение для плейсхолдера).
При этом метод `query()` вызывает какой-то другой метод библиотеки, 
тот ещё один и так далее, пока на очередном шаге не обнаружится ошибка и не будет выброшено исключение.
В консоле мы получим примерно следующее:

```php
DataNotEnough: Data elements (0) less than the placeholders in /test/my/db/Helpers/Template.php on line 126
 
Call Stack:
    1. {main}() /test/my/index.php:0
    2. callMyClass() /test/my/index.php:14
    3. MyClass->method() /test/my/index.php:11
    4. MyClass->selectDB() /test/my/MyClass.php:7
    5. go\DB\DB->query() /test/my/MyClass.php:20
    6. go\DB\DB->makeQuery() /test/my/db/DB.php:92
    7. go\DB\Helpers\Template->parse() /test/my/db/DB.php:312
    8. preg_replace_callback() /test/my/db/Helpers/Template.php:57
    9. go\DB\Helpers\Template->placeholderClb() /test/my/db/Helpers/Template.php:57
```

Мы видем, что исключение было выброшено со 126-й строки файла `Template.php`, однако это нам ничего не даёт.
Нам нужно видеть где мы совершили ошибку (строка 20 файла `MyClass.php`).
Эта запись находится в середине стека и нам придётся искать её, просматривая весь стек сверху вниз.
В идеале нам нужно получить сообщение:

```
DataNotEnough: Data elements (0) less than the placeholders in /test/my/MyClass.php on line 20
```

Нам нужно найти точку перехода из нашего кода в библиотеку и выкинуть все последующие вызовы.

## `truncate(array $options): bool`

Этот метод обрезает стек, как указано в опциях.
Проходит по вызовам начиная с первого до тех пор, пока не находит подходящий и удаляет всё начиная с него.
`$options` это массив, который может содержать следующие поля:

* `namespace`
* `class`
* `dir`
* `file`
* `filter`

Для каждого из этих параметров есть отдельный метод.
Например, `$trace->truncateByNamespace($ns)` это тоже самое, что и `$trace->truncate(['namespace' => $ns])`.
Значения этих параметров описываются в соответствующих методах.

Все эти методы возвращают `TRUE` в случае если стек был обрезан и `FALSE`, если совпадений не нашлось.

## `truncateByNamespace(string $namespace): bool`

Обрезает стек до момента входа в указанное пространство имён.
Для примера выше можно использовать:

```php
$trace->truncateByNamespace('go\DB');
echo $trace;
```

Результат:

```php
Call Stack:
1. {main}() /test/index.php:0
2. myfunc() /test/index.php:10
3. MyClass->sendQuery() /test/func/index.php:50
4. go\DB\DB->query() /test/MyClass.php:25
```

Момент неверного вызова: `/test/MyClass.php:25`

### `truncateByClass(string $classname): bool`

Обрезает до вызова метода указанного класса.

### `truncateByFile(string $filename): bool`

Обрезает до вызова кода расположенного в указанном файле.

### `truncateByDir(string $dirname): bool`

Аналогично, но до любого файла в внутри каталога.
`$dirname` используется как префикс, так что лучше указать завершающий слеш.

### `truncateByFilter(callable $filter): bool`

Вызывает пользовательскую функцию для каждого элемента стека начиная с самого раннего:

```php
$trace->truncateByFilter(function (array $call) {
    if ((isset($call['function'])) && ($call['function'] === 'query')) {
        return Trace::FILTER_LEFT;
    }
    return false;
});
```

Когда элемент по которому стоит резать стек найден, то функция должна вернуть одну из констант:

* `Trace::FILTER_LEFT` - удалить этот элемент и весь последующий стек
* `Trace::FILTER_LEAVE` - этот элемент оставить, а всё последующее удалить
