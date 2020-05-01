---
title: . Создание сопоставления приема в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается создание сопоставления приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 84ab277f5b0d4d1b2e09d31fb7c1254786affe6d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617737"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Создает сопоставление приема, связанное с определенной таблицей и конкретным форматом.

**Синтаксис**

`.create``table` *TableName* TableName `ingestion` *MappingKind* маппингкинд `mapping` *MappingName* *маппингформаттедасжсон*

> [!NOTE]
> * После создания на сопоставление можно ссылаться по имени в командах приема, вместо того чтобы указывать полное сопоставление как часть команды.
> * Допустимые значения для _маппингкинд_ : `CSV`, `JSON`, `avro` `parquet`, и`orc`
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
| mapping1 | CSV  | [{"Name": "RowNumber", "Тип_данных": "int", "Ксвдататипе": NULL, "Ordinal": 0, "ConstValue": NULL}, {"имя": "ROWGUID", "DataType": "строка", "Ксвдататипе": NULL, "Ordinal": 1, "ConstValue": NULL}] |

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о сопоставлении приема см. в разделе [сопоставления данных](mappings.md).
