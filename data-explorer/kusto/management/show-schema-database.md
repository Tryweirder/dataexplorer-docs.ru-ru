---
title: схема баз данных .show - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается схема schema баз данных .show в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 212aaf428e03190226a17509c97e9acd1394d2b1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519811"
---
# <a name="show-databases-schema"></a>схема баз данных .show

Возвращает плоский список структуры выбранных баз данных со всеми их таблицами и столбцов в одной таблице или объекте JSON.
При использовании с версией база данных возвращается только в том случае, если она является более поздней версией, чем предоставленная версия.

> [!NOTE]
> Версия должна предоставляться только в формате "vMM.mm". ММ представляет основную версию, а мм представляет второстепенную версию.

`.show``database` *База данныхИмя* `schema` и`details``if_later_than` *"Версия"* 

`.show``database` *База данныхИмя* `schema` `if_later_than` и `as` *"Версия"*`json`
 
`.show`База *данныхНаи1* `,` `databases` `(` `)` `schema` [`details` | `as` `json`]
 
`.show``databases` `,` *"Version"* *DatabaseName1* База данныхНаи1 if_later_than "Версия"... `(` `)` `schema` [`details` | `as` `json`]

**Пример** 
 
База данных 'TestDB' имеет 1 таблицу под названием "События".

```
.show database TestDB schema 
```

**Вывод примера**

|имя_базы_данных|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultКолобон|PrettyName|Версия
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|События|||True|False||       
|TestDB|События| Имя|System.String|True|False||     
|TestDB|События| StartTime|  System.DateTime|True|False||    
|TestDB|События| EndTime|    System.DateTime|True|False||        
|TestDB|События| Город|   System.String|True| False||     
|TestDB|События| SessionId|  System.Int32|True|  True|| 

**Пример** 
 
```
.show database TestDB schema if_later_than "v1.0" 
```
**Вывод примера**

|имя_базы_данных|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultКолобон|PrettyName|Версия
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|События|||True|False||       
|TestDB|События| Имя|System.String|True|False||     
|TestDB|События| StartTime|  System.DateTime|True|False||    
|TestDB|События| EndTime|    System.DateTime|True|False||        
|TestDB|События| Город|   System.String|True| False||     
|TestDB|События| SessionId|  System.Int32|True|  True||  

Поскольку была предоставлена версия ниже текущей версии базы данных, схема 'TestDB' была возвращена. Предоставление равной или более высокой версии привело бы к порожданию пустой результат.

**Пример** 
 
```
.show database TestDB schema as json
```

**Вывод примера**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

