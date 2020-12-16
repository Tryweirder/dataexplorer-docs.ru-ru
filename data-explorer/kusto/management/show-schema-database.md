---
title: . отображение схемы баз данных в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как отобразить схему баз данных в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4f3639aeb6e401aa37703bbef929af2275960a91
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513977"
---
# <a name="show-database-schema-commands"></a>. Отображение команд схемы базы данных

Следующие команды показывают схему базы данных в виде таблицы, объекта JSON или скрипта CSL.

## <a name="show-databases-schema"></a>. отображение схемы баз данных

**Синтаксис**

`.show``database` *DatabaseName* `schema` [ `details` ] [ `if_later_than` *"версия"*] 

`.show``databases` `(` *DatabaseName1* `,` .. `)` . `schema``details` 
 
`.show``databases` `(` *DatabaseName1* if_later_than *"версия"* `,` ... `)` `schema``details`

**Возвращает**

Возвращает плоский список структуры выбранных баз данных со всеми таблицами и столбцами в одной таблице или объекте JSON.
При использовании с версией база данных возвращается только в том случае, если она имеет более позднюю версию, чем указанная версия.

> [!NOTE]
> Версия должна предоставляться только в формате "vMM.mm". MM представляет основную версию, а mm — дополнительный номер версии.

**Пример** 
 
В базе данных "TestDB" имеется одна таблица с именем "Events".

```kusto
.show database TestDB schema 
```

**Выходные данные**

|имя_базы_данных|TableName|ColumnName|ColumnType|исдефаулттабле|исдефаултколумн|преттинаме|Версия
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||Версия 1.1       
|TestDB|События|||Верно|Неверно||       
|TestDB|События| Имя|System.String|Верно|Неверно||     
|TestDB|События| StartTime|  System.DateTime|Верно|Неверно||    
|TestDB|События| EndTime|    System.DateTime|Верно|Неверно||        
|TestDB|События| City|   System.String|Верно| Неверно||     
|TestDB|События| SessionId|  System.Int32|Да|  Да|| 

**Пример** 

В следующем примере база данных возвращается только в том случае, если она имеет более позднюю версию, чем указанная версия.
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**Выходные данные**

|имя_базы_данных|TableName|ColumnName|ColumnType|исдефаулттабле|исдефаултколумн|преттинаме|Версия
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||Версия 1.1       
|TestDB|События|||Верно|Неверно||       
|TestDB|События| Имя|System.String|Верно|Неверно||     
|TestDB|События| StartTime|  System.DateTime|Верно|Неверно||    
|TestDB|События| EndTime|    System.DateTime|Верно|Неверно||        
|TestDB|События| City|   System.String|Верно| Неверно||     
|TestDB|События| SessionId|  System.Int32|Да|  Да||  

Так как указана версия, которая ниже текущей версии базы данных, была возвращена схема "TestDB". Если указать равную или более позднюю версию, будет создан пустой результат.

## <a name="show-database-schema-as-json"></a>. показывать схему базы данных как JSON

**Синтаксис**

`.show``database` *DatabaseName* `schema` [ `if_later_than` *"версия"*] `as``json`
 
`.show``databases` `(` *DatabaseName1* `,` . `)` . `schema` . `as``json`
 
`.show``databases` `(` *DatabaseName1* if_later_than *"версия"* `,` ... `)` `schema` `as``json`

**Возвращает**

Возвращает плоский список структуры выбранных баз данных со всеми таблицами и столбцами в виде объекта JSON.
При использовании с версией база данных возвращается только в том случае, если она имеет более позднюю версию, чем указанная версия.

**Пример** 
 
```kusto
.show database TestDB schema as json
```

**Выходные данные**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>. показывать схему базы данных как скрипт CSL

Создает скрипт CSL со всеми необходимыми командами для создания копии заданной (или текущей) схемы базы данных.

**Синтаксис**

`.show``database`  `schema` DatabaseName `as` `csl` `script` [ `with(` *Параметры* `)` ]

**Аргументы**

Следующие *Параметры* являются необязательными.

* `IncludeEncodingPolicies`: ( `true`  |  `false` ) — Если `true` будут включаться политики кодирования на уровне базы данных, таблицы или столбца. По умолчанию имеет значение `false`. 
* `IncludeSecuritySettings`: ( `true`  |  `false` ) — Значение по умолчанию — `false` . Если `true` задано значение, будут включаться следующие параметры:
  * Полномочные субъекты на уровне базы данных или таблицы.
  * Политики безопасности на уровне строк на уровне таблицы.
  * Ограниченные политики доступа к представлениям на уровне таблицы.
* `IncludeIngestionMappings`: ( `true`  |  `false` ) — Если `true` будут включаться сопоставления приема на уровне таблицы. По умолчанию имеет значение `false`. 

**Возвращает**

Скрипт, возвращаемый в виде строки, будет содержать:

* Команды для создания всех таблиц в базе данных.
* Команды для установки всех политик базы данных, таблиц и столбцов в соответствии с исходными политиками.
* Команды для создания или изменения всех определяемых пользователем функций в базе данных.

**Пример** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
