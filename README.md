# Документация по axy-библиотекам на русском

Английская документация обычно лежит прямо в репе библиотеки в каталоге `doc` или в корневом `README.md`.

* backtrace
* binary
* callbacks
* cli-bin
* codecs
    * codecs-base64vlq
* config
* creator
* crontab
* crypt
* env
* envnorm
* errors
* evil
* fs
    * fs-ifs
    * fs-paths
    * fs-real
* htpasswd-cli
* magic
* mime
* min-html
* patterns
* phpcode
    * phpcode-compile
    * phpcode-phpdoc    
* random
* scache
    * scache-icache
    * scache-local
    * scache-stub
* sourcemap
* syspath

## Наименование

* Библиотеки ставятся через composer, им соответствуют пакеты с именами `axy/{name}` (например, `axy/backtrace`)
* Исходники лежат на github.com по адресу `/axypro/{name}`
* Классы конкретной библиотеки обединяются в пространстве имён `axy\{name}`, все пространства имён пишутся строчными буквами
* Юнит-тесты лежат в `axy\{name}\tests` 
* Вложенные пакеты занимают вложенные пространства имён, а в именах пакетов используется дефис. 
Например, `axy\scache\local`, пакет `axy\scache-local`, репа `axypro/scache-local` 
