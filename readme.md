# Синхронизация хранилища 1С с репозиторием git

Обсудить [![Join the chat at https://gitter.im/EvilBeaver/oscript-library](https://badges.gitter.im/EvilBeaver/oscript-library.svg)](https://gitter.im/EvilBeaver/oscript-library?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![GitHub release](https://img.shields.io/github/release/oscript-library/gitsync.svg)](https://github.com/oscript-library/gitsync/releases)

## Введение
Проект является глубоким рефакторингом утилиты v83unpack ([https://github.com/xDrivenDevelopment/v83unpack](https://github.com/xDrivenDevelopment/v83unpack)).

Изначально данный механизм представляет собой внешнюю обработку 1С:Предприятия, которая впоследствии была портирована на OneScript. [Исходные коды порта](https://github.com/xDrivenDevelopment/v83unpack/tree/develop/src/oscript) доступны в том же репозитории v83unpack.

Приложение *gitsync* представляет собой отдельное (standalone) приложение на 1Script, и предназначено для синхронизации хранилища конфигураций 1С с репозитарием git.

## Установка

### Вручную

1. Вручную - Скопировать каталог **gitsync/src** на жесткий диск
2. Запустить приложение командой `oscript.exe <каталог gitsync>\src\gitsync.os`

### Через пакетный менеджер opm

1. командой `opm install gitsync`
2. Запустить командой `gitsync`

# Использование

Возможны следующие сценарии использования/организации хранения исходников в репозитариях Git:

1. в одном репозитарии Git хранятся как исходники конфигурации 1С, так и прочие исходники/файлы продукта (например, документация, тесты и прочее) - `рекомендуется`
  + Используем этапы:
    + Клонировать средствами Git исходный репозитарий (`git clone`) или создать новый (`git init`)
    + `Выполнение настройки для начала выгрузки`
    + `Синхронизация` или `Экспорт исходников конфигурации в сторонний репозитарий Git`
    + далее штатные инструменты Git - `commit/pull/push` 
  
2. в одном репозитарии хранятся только исходники конфигурации 1С - `не рекомендуется`
  +  Используем этапы: 
    + `Подготовка нового репозитария` - если репозитария Гит еще нет
    + `Клонирование существующего пустого репо` - если уже был создан пустой репозитарий Git
    + `Выполнение настройки для начала выгрузки`
    + `Синхронизация`

## Экспорт исходников конфигурации в сторонний репозитарий Git
Основной режим работы для сценария `в одном репозитарии Git хранятся как исходники конфигурации 1С, так и прочие исходники/файлы продукта (например, документация, тесты и прочее)`.

+ Клонировать средствами Git исходный репозитарий (`git clone`) или создать новый (`git init`)
+ Перейти в каталог репозитария Git
+ Запустить gitsync с параметрами ```gitsync export <каталог или файл хранилища>  <КаталогИсходниковВнутриЛокальнойКопииGit> [-email домен почты пользователей]```

Будет инициализирован новый репо и созданы необходимые файлы для синхронизации.

Примеры использования:
+ `gitsync export "W:\Хранилище1С" src\config` - помещение исходников конфигурации в каталог `src\config` репозитория Git в текущем каталоге
+ `gitsync export "W:\Хранилище1С\1cv8ddb.1cd" src` - помещение исходников конфигурации в каталог `src` репозитория Git в текущем каталоге

## Подготовка нового репозитария

Запустить gitsync с параметрами ```gitsync init <каталог или файл хранилища> <локальный каталог git> [-email домен почты пользователей]```

Буден инициализирован новый репо и созданы необходимые файлы для синхронизации.

Примеры использования:
+ `gitsync init "W:\Хранилище1С" .`
+ `gitsync init "W:\Хранилище1С\1cv8ddb.1cd" W:\GitRepo`

## Клонирование существующего пустого репо

Часто бывает, что удаленный репо уже создан и нужно наполнить его служебными файлами синхронизатора.

Запустить gitsync с параметрами ```gitsync clone <каталог или файл хранилища> <url-git> [локальный каталог git] [-email домен почты пользователей]```

Буден клонирован удаленный репо и созданы необходимые файлы для синхронизации, если их там еще нет.

Примеры использования:
+ `gitsync clone "W:\Хранилище1С\1cv8ddb.1cd" W:\GitRepo` - помещение исходников конфигурации в корень репозитория Git

+ `gitsync clone "W:\Хранилище1С" .` - помещение исходников конфигурации в корень репозитория Git в текущем каталоге

## Выполнение настройки для начала выгрузки

### Файлы настройки

Для настройки выгрузки используются 2 файла:
+ `VERSION` - содержит номер текущей выгруженной версии хранилища 1С
+ `AUTHORS` - содержит информацию о связке пользователей хранилища 1С и пользователей репозитария Git

Указанные файлы должны находиться в корне каталога выгрузки исходников, который может не совпадать с корнем репозитария Git в зависимости от выбранного сценария организации хранения исходников.

Файл `VERSION` имеет формат xml-файла

Пример файла, в котором указано, что выгружено 10 версий:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<VERSION>10</VERSION>
```

Файл `AUTHORS` имеет формат ini-файла
```ini
Администратор=Пользователь1 <admin-user@mail.com>
Вася Иванов=Другой Пользователь <user-user@mail.com>
```
слева указано имя пользователя хранилища 1С

справа - представление имени пользователя репозитария Git и его e-mail

С помощью e-mail выполняется связка пользователя с публичными репозитариями (например, Github или Bitbucket)

### Шаги настройки

+ В файле `VERSION` указать версию, после которой будет выполняться выгрузка в Git
  + команда `gitsync set-version КаталогФайлаВерсии НомерВерсии`
  + Для выгрузки всего хранилища 1С нужно указать число `0` вместо `НомерВерсии`
  + для выгрузки с 50 версии нужно указать число `49` вместо `НомерВерсии`
  
+ В файле `AUTHORS` прописать сопоставление пользователей хранилища 1С и пользователей Git

## Синхронизация
Основной режим работы для сценария `Хранение исходников конфигурации 1С в отдельном репозитарии Git`. Аргументы командной строки для запуска:

* <каталог или файл хранилища>
* <адрес (url) репозитария>
* [Каталог исходников внутри локальной копии git-репозитария]
* [-email домен почты пользователей]
* [-v8version маска версии 1С] - маска версии в стиле стартера (8.3 или 8.3.5 или 8.2.19.109)

Пример:

    cd local-git-repo
    gitsync c:\storage\zup http://github.com/myAccount/zup.git src\config -v8version 8.3.6
  
  или
    cd local-git-repo
    gitsync c:\storage\zup http://github.com/myAccount/zup.git -v8version 8.3.6

## Получение справки

Справку по синтаксису команды можно получить, запустив `gitsync help <команда>`. Например:

    gitsync help clone
    gitsync help init

## Команды

  ```
  Синхронизация хранилища конфигураций 1С с репозитарием GIT.
  Использование:
  	gitsync <storage-path> <git-url> [local-dir] [ключи]
  	gitsync <команда> <параметры команды> [ключи]
  Параметры:
   <ПутьКХранилищу> - Файловый путь к каталогу хранилища конфигурации 1С.
   <URLРепозитория> - Адрес удаленного репозитория GIT.
   <ЛокальныйКаталогГит> - Каталог исходников внутри локальной копии git.
   -email - <домен почты для пользователей git>
   -v8version - Маска версии платформы (8.3, 8.3.5, 8.3.6.2299 и т.п.)
   -debug - <on|off>
   -verbose - <on|off>
   -branch - <имя ветки git>
   -format - <hierarchical|plain>
   -countupush - <количество комитов после которых отправлять в гит>
   -RegEx - <регулярное выражение для проверки комментария в хранилище>

  Возможные команды:
   clone        - Клонирует существующий репозиторий и создает служебные файлы
   init         - Создает новый репозиторий и создает служебные файлы
   all          - Запускает синхронизацию по нескольким репозиториям
   set-version  - Устанавливает необходимую версию в файл VERSION
   help         - Вывести справку по параметрам команды
   export       - Выполнить локальную синхронизацию, без pull/push
  ```

# Синхронизация по нескольким хранилищам

Зачастую удобно настроить регламентную (по расписанию) синхронизацию сразу по нескольким хранилищам 1С. Для этого необходимо подготовить конфигурационный файл с параметрами синхронизации ([пример файла](config-example.xml))

Далее необходимо запустить gitsync с командой all

    gitsync all <путь к xml-файлу конфигурации>

Подробнее о параметрах команды `all` можно прочитать, запустив `gitsync help all`
