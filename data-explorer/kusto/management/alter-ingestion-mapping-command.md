---
title: . изменение сопоставления приема — Azure обозреватель данных
description: В этой статье описывается изменение сопоставления приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f62692c7f5a1b557038f452f5ed3c023ec9849f9
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329033"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Изменяет существующее сопоставление приема, связанное с определенной таблицей, и указанным форматом (полное сопоставление Replace).

**Синтаксис**

`.alter``table` *TableName* `ingestion` *маппингкинд* `mapping` *MappingName* *маппингформаттедасжсон*

> [!NOTE]
> * На это сопоставление можно ссылаться по имени по командам приема, вместо того чтобы указывать полное сопоставление как часть команды.
> * Допустимые значения для _маппингкинд_ : `CSV` , `JSON` , `avro` , `parquet` и `orc` .

**Пример** 
 
```kusto
.alter table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"} }'
']'

.alter table MyTable ingestion json mapping "Mapping1"
'['
'   { "column" : "rownumber", "Properties":{"Path":"$.rownumber"}},'
'   { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
