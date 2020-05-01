---
title: . изменение сопоставления приема — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается изменение сопоставления приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f43039ff3935edbb6e92627d2f96b1c411e1ffa
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617822"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Изменяет существующее сопоставление приема, связанное с определенной таблицей, и указанным форматом (полное сопоставление Replace).

**Синтаксис**

`.alter``table` *TableName* TableName `ingestion` *MappingKind* маппингкинд `mapping` *MappingName* *маппингформаттедасжсон*

> [!NOTE]
> * На это сопоставление можно ссылаться по имени по командам приема, вместо того чтобы указывать полное сопоставление как часть команды.
> * Допустимые значения для _маппингкинд_ : `CSV`, `JSON`, `avro`, `parquet`и `orc`.

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
| mapping1 | CSV  | [{"Name": "RowNumber", "Тип_данных": "int", "Ксвдататипе": NULL, "Ordinal": 0, "ConstValue": NULL}, {"имя": "ROWGUID", "DataType": "строка", "Ксвдататипе": NULL, "Ordinal": 1, "ConstValue": NULL}] |
