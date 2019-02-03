# `Disabled`

Попытка доступа к отключённому сервису.
То есть сервис в принципе существует, но в данный момент или в данной конфигурации или в текущем окружении он отключён.
Под сервисом может пониматься всё, что угодно.
Внешний сервис или просто какая-то библиотека или объект.

```php
$application->getThatService()->doAction(); // Service "that" is disabled
```

## Методы

* `__construct([string $service [, Exception $previous [, mixed $thrower])`
    * `$service` - сам сервис (объект) или его название
* `getService(): ?string`
