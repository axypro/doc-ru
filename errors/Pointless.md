# `Pointless`

Бессмысленная в данном контексте операция.

Например, контейнер накапливает в себе последовательность неких данных.
Реализован `ArrayAccess` для того, чтобы иметь возможность добавлять данные следующим образом:

```php
$container[] = 'item';
$container[] = 'other item';
$container[] = 'next item';
```

Но `ArrayAccess` позволяет работать с отдельными элементами по индексам/ключам, что в данном контексте бессмысленно.

```php
unset($container['key']); // This action is pointless in the current context 
```
