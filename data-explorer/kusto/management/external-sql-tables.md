---
title: Создание и изменение внешних таблиц SQL в Azure обозреватель данных
description: В этой статье описывается создание и изменение внешних таблиц SQL.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 235c68a8a04fd76dd3a9e25abac63db09e00919a
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863342"
---
# <a name="create-and-alter-external-sql-tables"></a>Создание и изменение внешних таблиц SQL

Создает или изменяет внешнюю таблицу SQL в базе данных, в которой выполняется команда.  

## <a name="syntax"></a>Синтаксис

( `.create`  |  `.alter` ) `external` `table` *TableName* ([ColumnName: columnType],...)  
`kind` `=` `sql`  
`table``=` *Склтабленаме*  
`(`*склсерверконнектионстринг*`)`  
[ `with` `(` [ `docstring` `=` *Документация*] [ `,` `folder` `=` *имя_папки*], *property_name* `=` *значение* `,` ... `)` ]

## <a name="parameters"></a>Параметры

* *TableName* — имя внешней таблицы. Должны соответствовать правилам для [имен сущностей](../query/schema-entities/entity-names.md). Имя внешней таблицы не может совпадать с именем обычной таблицы в той же базе данных.
* *Склтабленаме* — имя таблицы SQL.
* *Склсерверконнектионстринг* — строка подключения к SQL Server. Может быть одним из следующих методов: 
  * *Встроенная проверка подлинности AAD* ( `Authentication="Active Directory Integrated"` ): пользователь или приложение проходят проверку подлинности с помощью AAD в Kusto, и тот же токен используется для доступа к конечной точке сети SQL Server.
  * *Проверка подлинности имени пользователя и пароля* ( `User ID=...; Password=...;` ). Если внешняя таблица используется для [непрерывного экспорта](data-export/continuous-data-export.md), необходимо выполнить проверку подлинности с помощью этого метода. 

> [!WARNING]
> Строки подключения и запросы, содержащие конфиденциальные сведения, должны быть замаскированы, чтобы их можно было опустить из любой трассировки Kusto. Дополнительные сведения см. в разделе [маскированные строковые литералы](../query/scalar-data-types/string.md#obfuscated-string-literals).

## <a name="optional-properties"></a>Необязательные свойства

| Свойство            | Тип            | Описание                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | Папка таблицы.                  |
| `docString`         | `string`        | Строка, задокументированная в таблице.      |
| `firetriggers`      | `true`/`false`  | `true`Значение указывает, что целевая система должна срабатывать триггеры INSERT, определенные в таблице SQL. Значение по умолчанию — `false`. (Дополнительные сведения см. в разделе [BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx) и [System. Data. SqlClient. SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)). |
| `createifnotexists` | `true`/ `false` | Если `true` значение равно, то Целевая таблица SQL будет создана, если она еще не существует; в `primarykey` этом случае необходимо указать свойство, чтобы указать столбец результата, являющийся первичным ключом. Значение по умолчанию — `false`.  |
| `primarykey`        | `string`        | Если `createifnotexists` имеет значение `true` , то результирующее имя столбца будет использоваться как первичный ключ таблицы SQL, если он создан с помощью этой команды.                  |

> [!NOTE]
> * Если таблица существует, `.create` команда завершится ошибкой. Используйте `.alter` для изменения существующих таблиц. 
> * Изменение схемы или формата внешней таблицы SQL не поддерживается. 

Требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md) для `.create` и [разрешения администратора таблицы](../management/access-control/role-based-authorization.md) для `.alter` . 
 
**Пример** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**Выходные данные**

| TableName   | TableType | Папка         | DocString | Свойства                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| екстерналскл | SQL       | екстерналтаблес | Документы      | {<br>  "Таржетентитикинд": "склтабле" ",<br>  "Таржетентитинаме": "Мисклтабле",<br>  "Таржетентитиконнектионстринг": "Server = TCP:мисервер. Database. Windows. NET, 1433; Authentication = Active Directory интегрирован; исходный каталог = MyDatabase; ",<br>  "Фиретригжерс": true,<br>  "CreateIfNotExists": true,<br>  "PrimaryKey": "x"<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>Запрос к внешней таблице типа SQL 

Поддерживается выполнение запросов к внешней таблице SQL. См. раздел [запросы к внешним таблицам](../../data-lake-query-data.md). 

> [!Note]
> Реализация запроса к внешней таблице SQL выполнит полное "SELECT *" (или выберите соответствующие столбцы) из таблицы SQL. Остальная часть запроса будет выполняться на стороне Kusto. 

Рассмотрим следующий запрос к внешней таблице: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto будет выполнять запрос SELECT * из таблицы в базе данных SQL, за которым следует счетчик на стороне Kusto. В таких случаях производительность должна быть лучше, если написать непосредственно в T-SQL напрямую ("SELECT COUNT (1) из таблицы") и выполнить с помощью [подключаемого модуля sql_request](../query/sqlrequestplugin.md), а не с помощью функции внешней таблицы. Аналогичным образом фильтры не отправляются в SQL запрос.  

Используйте внешнюю таблицу для запроса таблицы SQL, если запрос требует считывания всей таблицы (или соответствующих столбцов) для дальнейшего выполнения на стороне Kusto. Если запрос SQL можно оптимизировать в T-SQL, используйте [подключаемый модуль sql_request](../query/sqlrequestplugin.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Команды для общего управления внешней таблицей](externaltables.md)
* [Создание и изменение внешних таблиц в службе хранилища Azure или Azure Data Lake](external-tables-azurestorage-azuredatalake.md)
