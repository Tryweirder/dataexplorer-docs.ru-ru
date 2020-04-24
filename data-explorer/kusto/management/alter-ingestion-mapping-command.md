---
title: .alter ingestion отображение - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается отображение .alter ingestion в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5343d55fadafce552c5d837e5eb50763ccf45a4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522412"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Изменяет существующее отображение приема, связанное с конкретной таблицей и конкретным форматом (полное изменение отображения).

**Синтаксис**

`.alter``table` *TableName* `ingestion` *КартированиеКинд* `mapping` *КартированиеИмя* *КартированиеФорматоAsJson*

> [!NOTE]
> * Это отображение может быть отсылкано по названию командами приема, вместо указания полного отображения как части команды.
> * Действительные значения для _MappingKind_ `CSV` `avro`являются: `orc`, `JSON`, `parquet`, и .

**Пример** 
 
```
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
**Вывод примера**

| Имя     | Вид | Сопоставление                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| отображение1 | CSV  | «Имя»:«Rownumber»,»,«DataType»:«int»,,»CsvDataType»:null,«Ordinal»:0,«ConstValue»: null», «Имя»:«rowguid»,«DataType»:«строка»», «CsvDataType»: null,«Ordinal»: |