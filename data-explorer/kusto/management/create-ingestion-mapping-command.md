---
title: . Создание сопоставления приема — Azure обозреватель данных
description: В этой статье описывается создание сопоставления приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3855d56ad31bbf98a6a075feb44a598b3bdbf52a
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329067"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Создает сопоставление приема, связанное с определенной таблицей и конкретным форматом.

**Синтаксис**

`.create``table` *TableName* `ingestion` *маппингкинд* `mapping` *MappingName* *маппингформаттедасжсон*

> [!NOTE]
> * После создания на сопоставление можно ссылаться по имени в командах приема, вместо того чтобы указывать полное сопоставление как часть команды.
> * Допустимые значения для _маппингкинд_ : `CSV` , `JSON` , `avro` , `parquet` и`orc`
> * Если для таблицы уже существует сопоставление с таким именем:
>    * `.create`завершится ошибкой
>    * `.create-or-alter`изменит существующее сопоставление
 
**Пример** 
 
```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create-or-alter table MyTable ingestion json mapping "Mapping1"
'['
'    { "column" : "rownumber", "datatype" : "int", "Properties":{"Path":"$.rownumber"}},'
'    { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о сопоставлении приема см. в разделе [сопоставления данных](mappings.md).
