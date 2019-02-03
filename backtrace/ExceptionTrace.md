# `ExceptionTrace` - место исключения

`axy\backtrace\ExceptionTrace` расширяет класс [Trace](Trace.md).
Его объект в дополнение к стеку содержит также файл и строку на которой было выброшено исключение.

### `__construct([array $items [, string $file, int $line])`

* `$items` - см. [Trace](Trace.md)
* `$file` и `$line` - место выброса исключения

Из объекта исключения эти значения можно получить с помощью `$e->getTrace()`, `$e->getFile()` и `$e->getLine()`.

Если ни один аргумент не используется, то берётся место создания объекта:

```php
/* File /test/index.php */
use axy\backtrace\ExceptionTrace;

function one()
{
    return two();
}

function two()
{
    return new ExceptionTrace(); // line 15
}

$trace = one();

echo 'File: '.$trace->file.\PHP_EOL;
echo 'Line: '.$trace->line.\PHP_EOL;
echo $trace;
```

Вывод:

```
File: /test/index.php
Line: 15
#0 /test/index.php(10): two()
#1 /test/index.php(18): one()
#2 {main}
```

Если указать `$items`, но не указать `$file` и `$line`, они будут взяты из текушего вызова в стеке.

### Магические свойства (только для чтения)

В наследство от `Trace`:

* `$items`
* `$originalItems`

Новые:

* `$file`
* `$line`
* `$originalFile`
* `$originalLine`

`$original*` не изменяются, остальные могут быть изменены с помощью методов класса.

### `truncate*()`

См. [Trace::truncate()](truncate.md).

Дополнительно `$file` и `$line` заменяются на точку входа в библиотеку (последний необрезанный вызов).

### `trimFilename(string $dir)`

В дополнение к обрезанию файлов в стеке, обрезает также `$file`.
