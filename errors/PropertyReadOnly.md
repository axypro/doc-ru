# `PropertyReadOnly`

Попытка записать значение в поле контейнера (обычно в магическое свойство объека), которое является только для чтения.

В отличии от [ContainerReadOnly](ContainerReadOnly.md) имеется ввиду, что контейнер в принципе открыт для изменений,
но конретно это поле нет.

## Методы

* `__construct([mixed $container [, string $key [, Exception $previous [, mixed $thrower])`
    * `$container` - сам контейнер (объект) или его название
    * `$key` - имя поля
* `getContainer(): ?mixed`
* `getKey(): ?string`
