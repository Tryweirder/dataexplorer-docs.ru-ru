---
title: Команды для последующих кластеров — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются команды последующих кластеров в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 2ca15e1970ab785bfd5da8623f3dcc569576f1d9
ms.sourcegitcommit: 2ee2901cb82e1655b7f0d960d3427da084230731
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94520570"
---
# <a name="cluster-follower-commands"></a>Команды для последующих кластеров

Ниже перечислены управляющие команды для управления конфигурацией кластера следующих элементов. Эти команды выполняются синхронно, но применяются при следующем периодическом обновлении схемы. Таким образом, задержка может составлять несколько минут, пока не будет применена новая конфигурация.

К командам, приведенным ниже, относятся [команды уровня базы данных](#database-level-commands) и [команды уровня таблицы](#table-level-commands).

## <a name="database-level-commands"></a>Команды уровня базы данных

### <a name="show-follower-database"></a>. отображение базы данных следующих

Отображает базу данных (или базы данных), за которой следует другой ведущий кластер, для которого настроено одно или несколько переопределений уровня базы данных.

**Синтаксис**

`.show``follower` `database` *DatabaseName*

`.show``follower` `databases` `(` *DatabaseName1* `,` ...`,` *Датабасенамен*`)`

**Выходные данные** 

| Выходной параметр                     | Тип    | Описание                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| имя_базы_данных                         | Строка  | Имя базы данных, за которой идет подписка.                                                                           |
| леадерклустерметадатапас            | Строка  | Путь к контейнеру метаданных кластера лидера.                                                               |
| качингполициоверриде                | Строка  | Переопределяющая политика кэширования для базы данных, сериализованная как JSON или null.                                         |
| аусоризедпринЦипалсоверриде         | Строка  | Переопределение коллекции полномочных субъектов для базы данных, сериализованных как JSON или null.                    |
| аусоризедпринЦипалсмодификатионкинд | Строка  | Тип изменения, применяемый с помощью АусоризедпринЦипалсоверриде `none` ( `union` или `replace` ).                  |
| качингполиЦиесмодификатионкинд      | Строка  | Тип изменения для применения с использованием переопределений политики кэширования базы данных или уровня таблицы ( `none` `union` или `replace` ). |
| исаутопрефетченаблед                | Логическое значение | Будут ли предварительно получены новые данные при каждом обновлении схемы.        |
| таблеметадатаоверридес               | Строка  | Сериализация JSON переопределений свойств уровня таблицы (если они определены).                                      |

### <a name="alter-follower-database-policy-caching"></a>. изменение кэширования политики базы данных

Изменяет политику кэширования базы данных-следов, чтобы переопределить набор, заданный для базы данных-источника в кластере лидера. Для этого требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md).

**Примечания**

* По умолчанию `modification kind` для политик кэширования задано значение `union` . Чтобы изменить `modification kind` команду, воспользуйтесь командой [. ALTER "база данных: кэширование — политики-изменение типа](#alter-follower-database-caching-policies-modification-kind) ".
* Просмотр политики или действующих политик после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение срока хранения политики базы данных](../management/retention-policy.md#show-retention-policy)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Просмотр параметров переопределения в базе данных последующих действий после внесения изменений можно выполнить с помощью [. отобразить базу данных-след](#show-follower-database)

**Синтаксис**

`.alter``follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *хотдатаспан*

**Пример**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>. Удалите кэширование политики базы данных за преследом

Удаляет политику кэширования, переопределяющую базу данных. Это приводит к тому, что политика, заданная в базе данных источника в кластере лидера, является действующей.
Для этого требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md). 

**Примечания**

* Просмотр политики или действующих политик после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение срока хранения политики базы данных](../management/retention-policy.md#show-retention-policy)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.delete``follower` `database` *DatabaseName* DatabaseName `policy``caching`

**Пример**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>. Добавление участников базы данных-участника

Добавляет полномочных участников в коллекцию баз данных последующих участников, переопределяющих полномочные субъекты. Для этого требуется [разрешение датабасеадмин](../management/access-control/role-based-authorization.md).

**Примечания**

* По умолчанию `modification kind` для таких полномочных субъектов используется `none` . Чтобы изменить параметр `modification kind` использовать  [базу данных-участник ALTER](#alter-follower-database-principals-modification-kind), измените тип.
* Просмотр эффективной коллекции участников после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение участников базы данных](../management/security-roles.md#managing-database-security-roles)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.add``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) роль `(` *principal1* `,` ... `,` *принЦипалн* `)` [ `'` *Примечания* `'` ]

**Пример**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>. Удалите участников базы данных-следов

Удаляет полномочные субъекты из коллекции баз данных последующих участников, переопределяющих полномочные субъекты.
Для этого требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md).

**Примечания**

* Просмотр эффективной коллекции участников после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение участников базы данных](../management/security-roles.md#managing-database-security-roles)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.drop``follower` `database` *DatabaseName* ( `admins`  |  `users`  |  `viewers`  |  `monitors` ) `(` *principal1* `,` ... `,` *принЦипалн*`)`

**Пример**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>. изменение участников базы данных-участника — изменение типа

Изменяет тип изменения полномочных участников базы данных. Для этого требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md).

**Примечания**

* Просмотр эффективной коллекции участников после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение участников базы данных](../management/security-roles.md#managing-database-security-roles)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.alter``follower` `database` *DatabaseName* 
 `principals-modification-kind` = ( `none`  |  `union`  |  `replace` )

**Пример**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>. ALTER следующий кэш базы данных-политики-изменение-тип

Изменяет тип изменения политик базы данных и таблицы следующих типов. Для этого требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md).

**Примечания**

* Просмотр эффективной коллекции политик кэширования базы данных и уровня таблицы после изменения можно выполнить с помощью стандартных `.show` команд:
    * [. Отображение сведений о таблицах](show-tables-command.md)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.alter``follower` `database` *DatabaseName* `caching-policies-modification-kind` = ( `none`  |  `union`  |  `replace` )

**Пример**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>. Изменение предварительной выборки базы данных-экстентов

Перед тем как сделать эти данные запрашиваемыми, в кластере последов можно ожидать получения новых данных из базового хранилища в SSD (кэш) узлов.

Следующая команда изменяет конфигурацию базы данных-следов для предварительной выборки новых экстентов при каждом обновлении схемы. Для этой команды требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md).

> [!WARNING]
> * Этот параметр может понизить актуальность данных в базе данных последующих версий.
> * Конфигурация по умолчанию — `false` , и рекомендуется использовать значение по умолчанию.
> * При выборе изменения параметра на необходимо `true` оценить влияние на актуальность в течение некоторого периода времени после изменения конфигурации.

**Синтаксис**

`.alter``follower` `database` *DatabaseName* `prefetch-extents` = ( `true`  |  `false` )

`.alter``follower` `database` *DatabaseName* [ `from` `h@'` *путь к контейнеру метаданных кластера лидера* `'` ] `prefetch-extents` = ( `true`  |  `false` )

**Пример**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>Команды уровня таблицы

### <a name="alter-follower-table-policy-caching"></a>. ALTER, кэширование политики таблицы

Изменяет политику кэширования на уровне таблицы в базе данных-источнике, чтобы переопределить политику, заданную для базы данных-источника в кластере лидера.
Для этого требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md). 

**Примечания**

* Просмотр политики или действующих политик после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение срока хранения политики базы данных](../management/retention-policy.md#show-retention-policy)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.alter``follower` `database` *Имя базы* данных *TableName* `policy` `caching` `hot` `=` *хотдатаспан*

`.alter``follower` `database` *DatabaseName* Таблицы DatabaseName `(` *TableName1* `,` ... `,` *Табленамен* `)` `policy` `caching` `hot` `=` *Хотдатаспан*

**Пример**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>. Удалите кэширование политики для таблицы следов

Удаляет политику кэширования на уровне таблицы для базы данных-следов, делая ее действующей для базы данных-источника в кластере лидерства. Требуются [разрешения датабасеадмин](../management/access-control/role-based-authorization.md). 

**Примечания**

* Просмотр политики или действующих политик после изменения можно выполнить с помощью `.show` команд:
    * [. Отображение срока хранения политики базы данных](../management/retention-policy.md#show-retention-policy)
    * [. отобразить сведения о базе данных](../management/show-databases.md)
    * [.show table details](show-tables-command.md)
* Просмотр параметров переопределения в базе данных следующих действий после изменения можно выполнить с помощью [. отобразить базу данных за пределами](#show-follower-database)

**Синтаксис**

`.delete``follower` `database` *Имя_базы_данных* , `table` *TableName* TableName `policy``caching`

`.delete``follower` `database` *DatabaseName* `tables` `(` *TableName1* `,` ... `,` *Табленамен* `)` `policy``caching`

**Пример**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>Пример конфигурации

Ниже приведены примеры действий по настройке базы данных следующего типа.

В этом примере:

* Наш кластер ниже `MyFollowerCluster` будет использоваться в качестве базы данных `MyDatabase` в кластере лидерства `MyLeaderCluster` .
    * `MyDatabase` содержит `N` таблицы: `MyTable1` , `MyTable2` , `MyTable3` ,... `MyTableN` ( `N` > 3).
    * На `MyLeaderCluster`:
    
    | `MyTable1` Политика кэширования | `MyTable2` Политика кэширования | `MyTable3`...`MyTableN` Политика кэширования   | `MyDatabase` Полномочные субъекты                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | горячий диапазон данных = `7d`      | горячий диапазон данных = `30d`     | горячий диапазон данных = `365d`                   | *Средства просмотра*  =  `aadgroup=scubadivers@contoso.com` ; *Администраторы* = `aaduser=jack@contoso.com` |
     
    * `MyFollowerCluster`Мы хотим:
    
    | `MyTable1` Политика кэширования | `MyTable2` Политика кэширования | `MyTable3`...`MyTableN` Политика кэширования   | `MyDatabase` Полномочные субъекты                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | горячий диапазон данных = `1d`      | горячий диапазон данных = `3d`      | горячий диапазон данных = `0d` (ничего не кэшируется) | *Администраторы*  =  `aaduser=jack@contoso.com` , *средства просмотра* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> Оба `MyFollowerCluster` `MyLeaderCluster` значения и должны находиться в одном регионе.

### <a name="steps-to-execute"></a>Шаги для выполнения

*Предварительные требования:* Настройте кластер `MyFollowerCluster` для отслеживания базы данных `MyDatabase` из кластера `MyLeaderCluster` .

> [!NOTE]
> Участник, выполняющий команды управления, должен быть `DatabaseAdmin` в базе данных `MyDatabase` .

#### <a name="show-the-current-configuration"></a>Отображение текущей конфигурации

Ознакомьтесь с текущей конфигурацией, в соответствии с которой `MyDatabase` следует `MyFollowerCluster` :

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Столбец                              | Значение                                                    |
|-------------------------------------|----------------------------------------------------------|
|имя_базы_данных                         | MyDatabase                                               |
|леадерклустерметадатапас            | `https://storageaccountname.blob.core.windows.net/cluster` |
|качингполициоверриде                | null                                                     |
|аусоризедпринЦипалсоверриде         | []                                                       |
|аусоризедпринЦипалсмодификатионкинд | None                                                     |
|исаутопрефетченаблед                | Нет                                                    |
|таблеметадатаоверридес               |                                                          |
|качингполиЦиесмодификатионкинд      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>Переопределение полномочных участников

Замените коллекцию полномочных участников `MyDatabase` на ON `MyFollowerCluster` коллекцией, которая включает в себя только одного пользователя AAD в качестве администратора базы данных, и одного пользователя AAD в качестве средства просмотра базы данных:

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

Только эти два субъекта имеют разрешения на доступ к `MyDatabase``MyFollowerCluster`

```kusto
.show database MyDatabase principals
```

| Роль                       | принЦипалтипе | принЦипалдисплайнаме                        | принЦипалобжектид                    | принЦипалфкн                                                                      | Примечания |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| Администратор MyDatabase базы данных  | Пользователь AAD      | Гнездо Kusto (UPN: jack@contoso.com )       | 12345678-ABCD-ефеф-1234-350bf486087b | аадусер = 87654321-ABCD-EFEF-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |
| Средство просмотра MyDatabase базы данных | Пользователь AAD      | Jill Kusto (UPN: jack@contoso.com )       | abcdefab-abcd-efef-1234-350bf486087b | аадусер = 54321789-ABCD-EFEF-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| аадусер = 87654321-ABCD-EFEF-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |
| аадусер = 54321789-ABCD-EFEF-1234-350bf486087b; 55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>Переопределение политик кэширования

Замените коллекцию политик кэширования базы данных и уровня таблицы `MyDatabase` на ON `MyFollowerCluster` , установив *для* всех таблиц кэшированные данные, исключая две определенные таблицы — `MyTable1` , `MyTable2` — для которых будут кэшироваться данные для периодов `1d` и `3d` соответственно:

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

Данные кэшируются только для этих двух конкретных таблиц, а остальные — в `0d` следующих случаях:

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| качингполици                                                                | TableNames                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"Датахотспан": {"value": "1,00:00:00"}, "Индексхотспан": {"value": "1,00:00:00"}} | ["MyTable1"]                |
| {"Датахотспан": {"значение": "3.00:00:00"}, "Индексхотспан": {"value": "3.00:00:00"}} | ["MyTable2"]                |
| {"Датахотспан": {"значение": "0.00:00:00"}, "Индексхотспан": {"value": "0.00:00:00"}} | ["MyTable3",..., "Митаблен"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| таблеметадатаоверридес                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1": {"Качингполициоверриде": {"Датахотспан": {"value": "1,00:00:00"}, "Индексхотспан": {"value": "1,00:00:00"}}}} |
| {"MyTable2": {"Качингполициоверриде": {"Датахотспан": {"значение": "3.00:00:00"}, "Индексхотспан": {"value": "3.00:00:00"}}}} |

#### <a name="summary"></a>Сводка

См. текущую конфигурацию `MyDatabase` `MyFollowerCluster` :

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| Столбец                              | Значение                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|имя_базы_данных                         | MyDatabase                                                                                                                                                                      |
|леадерклустерметадатапас            | `https://storageaccountname.blob.core.windows.net/cluster`                                                                                                                        |
|качингполициоверриде                | {"Датахотспан": {"значение": "00:00:00"}, "Индексхотспан": {"value": "00:00:00"}}                                                                                                        |
|аусоризедпринЦипалсоверриде         | [{"Principal": {"FullyQualifiedName": "аадусер = 87654321-ABCD-EFEF-1234-350bf486087b",...}, {"субъект": {"FullyQualifiedName": "аадусер = 54321789-ABCD-EFEF-1234-350bf486087b",...}] |
|аусоризедпринЦипалсмодификатионкинд | Заменить                                                                                                                                                                         |
|исаутопрефетченаблед                | Нет                                                                                                                                                                           |
|таблеметадатаоверридес               | {"Митаржеттабле": {"Качингполициоверриде": {"Датахотспан": {"значение": "3.00:00:00"}...}, "Мисаурцетабле": {"Качингполициоверриде": {"DataHotSpan": {"value": "1,00:00:00"},...}}}       |
|качингполиЦиесмодификатионкинд      | Заменить                                                                                                                                                                         |
