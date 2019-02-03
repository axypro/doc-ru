# `Trace` - обёртка над стеком

### `__construct([array $items])`

Конструктор принимает массив элементов, сходный по формату с результатом [debug_backtrace()](http://php.net/debug_backtrace):

* Порядковый массив, где каждый элемент соответствует одному из вызовов функции/метода
* Первый (с индексом 0) элемент указывает на последний (текущий) вызов.
* Каждый вызов описывается массивом с полями (все необязательные):
    * `function`
    * `line`
    * `file`
    * `class`
    * `object`
    * `type`
    * `args`
    
Если не указать аргумент, то будет использован текущий стек:

```php
use axy\backtrace\Trace;

function x()
{
    return y();
}

function y()
{
    return new Trace();
}

$trace = x();
print_r($trace->items);
```

В результате получится:

```
[
    0 => [
        'file' => '/test/index.php',
        'line' => 9,
        'function' => 'y',
        'args' => [],
    ],
    1 => [
        'file' => '/test/index.php',
        'line' => 17,
        'function' => 'x',
        'args' => [],
    ],
]
```
     
### Магические свойства `$item` и `$originalItem`

Свойства только для чтения.
Изначально оба содержат в себе полученный в конструкторе стек.
При модификации стека методами класса меняется `$item`, а `$originalItem` всегда содержит оригинальные значения.

```php
$trace = new Trace();
$trace->truncateByLimit(5);
$trace->trimFilename('/var/lib/');

print_r($trace->items); // modified array
print_r($trace->originalItems); // original array
```

### `normalize(): void`

В элементах стека могут быть заполнены не все поля и при доступе к ним могут быть ошибки:

```php
// если это вызов простой функции (не метода), то поля class не будет, что приведёт к ошибке
echo 'class: '.$item['class']; 
```

`normalize()` заполняет все пропущенные поля элементов стека значениями по умолчанию.

### `truncateByLimit(int $limit): void`

Оставляет в стеке указанное количество последних вызовов.
Все более ранние удаляются.

### `trimFilename(string $dir): void`

Обрезает в названии всех файлов лежащих в указанном каталоге этот самый каталог.
Когда проект лежит где-то глубоко в файловой системе, вывод одинаковых длинных путей только мешает восприятию.
Например, было:

```
#0 /test/the/long/path/to/root/libs/func.php(5): getTrace()
#1 /test/the/long/path/to/root/index.php(6): func()
#2 /test/the/long/path/to/root/index.php(9): f(1)
#3 {main}
```

После же `$trace->trimFilename(__DIR__.'/')` стало:

```
#0 libs/func.php(5): getTrace()
#1 index.php(6): func()
#2 index.php(9): f(1)
#3 {main}
```

Аргумент `$dir` воспринимается просто как префикс.
Лучше его заверщать слешем.

### `truncate*()`-методы

Эти методы обрезают стек до нужного места.
См. отдельный [раздел](truncate.md).

### Магия

Класс реализует интерфейсы `ArrayAccess`, `Countable`, `Traversble`.
Можно работать с объектом, как с массивом вызовов (только для чтения):

```php
echo $trace[1]['line']; // тоже что и $trace->items[1]['line']
```

### `__toString(): string`

```php
echo $trace;
```

Возвращает текстовое представление модифицированного стека примерно в том же виде, как стек исключения выводится в консоль.
