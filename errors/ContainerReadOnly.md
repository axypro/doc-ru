# `ContainerReadOnly`

Попытка записать что-то в read-only контейнер.
Например, в объект с помощью магических свойств.

Запись в контейнер либо вообще не подразумевается (значения свойств вычисляются как-то внутри).
Либо происходит в конструкторе или на начальном этапе инициализации:

```php
$container->x = 5;
$container->y = 6;
$container->freeze();
$container->z = 7; // ContainerReadOnly exception
```

## Методы

* `__construct([string $container [, Exception $previous [, mixed $thrower])`
    * `$container` - сам контейнер (объект) или его название
* `getContainer(): ?mixed`
