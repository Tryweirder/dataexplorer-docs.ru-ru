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
ms.openlocfilehash: 90a48ec3a830e754bf823712ca7016d162474a39
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616989"
---
# <a name="show-databases-schema"></a>. отображение схемы баз данных

Возвращает плоский список структуры выбранных баз данных со всеми таблицами и столбцами в одной таблице или объекте JSON.
При использовании с версией база данных возвращается только в том случае, если она имеет более позднюю версию, чем указанная версия.

> [!NOTE]
> Версия должна предоставляться только в формате "vMM.mm". MM представляет основную версию, а mm — дополнительный номер версии.

`.show``database` *DatabaseName* DatabaseName `schema` [`details`] [`if_later_than` *"версия"*] 

`.show``database` *DatabaseName* DatabaseName `schema` [`if_later_than` *"версия"*] `as``json`
 
`.show``databases` `,` *DatabaseName1* DatabaseName1 `(` ... `)` `schema` [`details` | `as` `json`]
 
`.show``databases` *"Version"* `,` *DatabaseName1* DatabaseName1 if_later_than "версия"... `(` `)` `schema` [`details` | `as` `json`]

**Пример** 
 
База данных "TestDB" содержит 1 таблицу с именем "Events".

```kusto
.show database TestDB schema 
```

**Пример выходных данных**

|имя_базы_данных|TableName|ColumnName|ColumnType|исдефаулттабле|исдефаултколумн|преттинаме|Версия
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||Версия 1.1       
|TestDB|События|||True|False||       
|TestDB|События| Имя|System.String|True|False||     
|TestDB|События| StartTime|  System.DateTime|True|False||    
|TestDB|События| EndTime|    System.DateTime|True|False||        
|TestDB|События| Город|   System.String|True| False||     
|TestDB|События| SessionId|  System.Int32|True|  True|| 

**Пример** 
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```
**Пример выходных данных**

|имя_базы_данных|TableName|ColumnName|ColumnType|исдефаулттабле|исдефаултколумн|преттинаме|Версия
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||Версия 1.1       
|TestDB|События|||True|False||       
|TestDB|События| Имя|System.String|True|False||     
|TestDB|События| StartTime|  System.DateTime|True|False||    
|TestDB|События| EndTime|    System.DateTime|True|False||        
|TestDB|События| Город|   System.String|True| False||     
|TestDB|События| SessionId|  System.Int32|True|  True||  

Так как указана версия, которая ниже текущей версии базы данных, была возвращена схема "TestDB". Если указать равную или более позднюю версию, будет создан пустой результат.

**Пример** 
 
```kusto
.show database TestDB schema as json
```

**Пример выходных данных**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

