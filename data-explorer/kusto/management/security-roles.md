---
title: Управление ролями безопасности в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается управление ролями безопасности в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3b30a3e578b6bb1f21dedfcfec7629b60bb255f3
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324505"
---
# <a name="security-roles-management"></a>Управление ролями безопасности

> [!IMPORTANT]
> Прежде чем изменять правила авторизации в кластерах Kusto, прочитайте следующие [сведения: обзор управления доступом Kusto](../management/access-control/index.md)  
>  [на основе роли](../management/access-control/role-based-authorization.md) . 

В этой статье описываются управляющие команды, используемые для управления ролями безопасности.
Роли безопасности определяют, какие субъекты безопасности (пользователи и приложения) имеют разрешения на работу с защищенным ресурсом, таким как база данных или таблица, и какие операции разрешены. Например, участники, имеющие `database viewer` роль безопасности для конкретной базы данных, могут запрашивать и просматривать все сущности этой базы данных (за исключением таблиц с ограниченным доступом).

Роль безопасности может быть связана с субъектами безопасности или группами безопасности (у которых могут быть другие субъекты безопасности или другие группы безопасности). Когда участник безопасности пытается выполнить операцию с защищенным ресурсом, система проверяет, что участник связан по крайней мере с одной ролью безопасности, которая предоставляет разрешения на выполнение этой операции с ресурсом. Это называется **проверкой авторизации**. Если не выполнить проверку авторизации, операция будет прервана.

**Синтаксис**

Синтаксис команд управления ролями безопасности:

*Команда* *секураблеобжекттипе* *секураблеобжектнаме* *Role* [ `(` *листофпринЦипалс* `)` [*Описание*]]

* *Команда* указывает тип выполняемого действия: `.show` ,, и `.add` `.drop` `.set` .

    |*Команда* |Описание                                  |
    |-------|---------------------------------------------|
    |`.show`|Возвращает текущее значение или значения.         |
    |`.add` |Добавляет в роль один или несколько участников.     |
    |`.drop`|Удаляет из роли один или несколько участников.|
    |`.set` |Задает роль для конкретного списка участников, удаляя все предыдущие (если они есть).|

* *Секураблеобжекттипе* — это тип объекта, роль которого указана.

    |*секураблеобжекттипе*|Описание|
    |---------------------|-----------|
    |`database`|Указанная база данных|
    |`table`|Указанная таблица|
    |`materialized-view`| Указанное [материализованные представление](materialized-views/materialized-view-overview.md)| 

* *Секураблеобжектнаме* — имя объекта.

* *Role* — это имя соответствующей роли.

    |*Роль*      |Описание|
    |------------|-----------|
    |`principals`|Может использоваться только как часть `.show` глагола; возвращает список участников, которые могут повлиять на защищаемый объект.|
    |`admins`    |Управлять защищаемым объектом, включая возможность просмотра, изменения и удаления объекта и всех его подобъектов.|
    |`users`     |Может просматривать защищаемый объект и создавать новые объекты под ним.|
    |`viewers`   |Может просматривать защищаемый объект.|
    |`unrestrictedviewers`|Только на уровне базы данных, позволяет просматривать ограниченные таблицы (которые не предоставляются "нормальным" `viewers` и `users` ).|
    |`ingestors` |Только на уровне базы данных разрешите прием данных во всех таблицах.|
    |`monitors`  ||

* *ЛистофпринЦипалс* — это необязательный, разделенный запятыми список идентификаторов субъектов безопасности (значений типа `string` ).

* *Description* — это необязательное значение типа `string` , которое хранится вместе с Ассоциацией для будущих целей аудита.

## <a name="example"></a>Пример

Следующая команда управления выводит список всех субъектов безопасности, имеющих некоторый доступ к таблице `StormEvents` в базе данных:

```kusto
.show table StormEvents principals
```

Ниже приведены потенциальные результаты этой команды.

|Роль |принЦипалтипе |принЦипалдисплайнаме |принЦипалобжектид |принЦипалфкн 
|---|---|---|---|---
|Администратор Апсти базы данных |Пользователь Azure AD |Марк Смит |cd709aed-a26c-e3953dec735e |аадусер =msmith@fabrikam.com|





## <a name="managing-database-security-roles"></a>Управление ролями безопасности базы данных

`.set``database` *Имя_базы_данных* , *роль* `none` [ `skip-results` ]

`.set``database` *DatabaseName* *Role* - `(` *субъект* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

`.add``database` *DatabaseName* *Role* - `(` *субъект* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

`.drop``database` *DatabaseName* *Role* - `(` *субъект* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

Первая команда удаляет все субъекты из роли. Второй удаляет все субъекты из роли и задает новый набор участников. Третья добавляет новых участников к роли без удаления существующих участников. Последний удаляет указанные участники из ролей и сохраняет другие.

Где:

* *DatabaseName* — это имя базы данных, роль безопасности которой изменяется.

* *Роль* : `admins` , `ingestors` , `monitors` , `unrestrictedviewers` , `users` или `viewers` .

* *Участник* — это один или несколько участников. Сведения об указании этих участников см. в разделе [субъекты и поставщики удостоверений](./access-control/principals-and-identity-providers.md) .

* `skip-results`, если он указан, запрашивает, чтобы команда не возвращала обновленный список участников базы данных.

* *Описание*, если оно предоставлено, — это текст, который будет связан с изменением и получен соответствующей `.show` командой.

## <a name="managing-table-security-roles"></a>Управление ролями безопасности таблиц

`.set``table` *TableName* , *роль* `none` [ `skip-results` ]

`.set``table` *TableName* *Role* `(` , *субъект* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

`.add``table` *TableName* *Role* `(` , *субъект* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

`.drop``table` *TableName* *Role* `(` , *субъект* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

Первая команда удаляет все субъекты из роли. Второй удаляет все субъекты из роли и задает новый набор участников. Третья добавляет новых участников к роли без удаления существующих участников. Последний удаляет указанные участники из ролей и сохраняет другие.

Где:

* *TableName* — это имя таблицы, роль безопасности которой изменяется.

* *Роль* : `admins` или `ingestors` .

* *Участник* — это один или несколько участников. Сведения об указании этих участников см. в разделе [субъекты и поставщики удостоверений](./access-control/principals-and-identity-providers.md) .

* `skip-results`, если он указан, запрашивает, чтобы команда не возвращала обновленный список участников таблицы.

* *Описание*, если оно предоставлено, — это текст, который будет связан с изменением и получен соответствующей `.show` командой.

**Пример**

```kusto
.add table Test admins ('aaduser=imike@fabrikam.com ')
```

## <a name="managing-materialized-view-security-roles"></a>Управление материализованными представлениями безопасности

`.show``materialized-view` *Материализедвиевнаме*`principals`

`.set``materialized-view` *MaterializedViewName* `admins` Материализедвиевнаме `(` *Основной сервер* `,[` *Субъект...*`])`

`.add``materialized-view` *MaterializedViewName* `admins` Материализедвиевнаме `(` *Основной сервер* `,[` *Субъект...*`])`

`.drop``materialized-view` *MaterializedViewName* `admins` Материализедвиевнаме `(` *Основной сервер* `,[` *Субъект...*`])`

Где:

* *Материализедвиевнаме* — имя материализованных представлений, роль безопасности которой изменяется.
* *Участник* — это один или несколько участников. См. раздел [субъекты и поставщики удостоверений](./access-control/principals-and-identity-providers.md) .

## <a name="managing-function-security-roles"></a>Управление ролями безопасности функций

`.set``function` *FunctionName* , *роль* `none` [ `skip-results` ]

`.set``function` *FunctionName* *Role* - `(` *участник* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

`.add``function` *FunctionName* *Role* - `(` *участник* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

`.drop``function` *FunctionName* *Role* - `(` *участник* роли [ `,` *субъект*...] `)` [ `skip-results` ] [*Описание*]

Первая команда удаляет все субъекты из роли. Второй удаляет все субъекты из роли и задает новый набор участников. Третья добавляет новых участников к роли без удаления существующих участников. Последний удаляет указанные участники из ролей и сохраняет другие.

Где:

* *FunctionName* — имя функции, роль безопасности которой изменяется.

* *Роль* всегда `admin` .

* *Участник* — это один или несколько участников. Сведения об указании этих участников см. в разделе [субъекты и поставщики удостоверений](./access-control/principals-and-identity-providers.md) .

* `skip-results`, если он указан, запрашивает, чтобы команда не возвращала обновленный список субъектов-функций.

* *Описание*, если оно предоставлено, — это текст, который будет связан с изменением и получен соответствующей `.show` командой.

**Пример**

```kusto
.add function MyFunction admins ('aaduser=imike@fabrikam.com') 'This user should have access'
```

