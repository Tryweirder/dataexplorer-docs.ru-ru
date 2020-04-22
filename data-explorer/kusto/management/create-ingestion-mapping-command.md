---
title: .create ingestion отображение - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается отображение .createestion в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 10e656b074516ad8b0018e627d9904251aebbf10
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744494"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Создает отображение приема, связанное с определенной таблицей и определенным форматом.

**Синтаксис**

`.create``table` *TableName* `ingestion` *КартированиеКинд* `mapping` *КартированиеИмя* *КартированиеФорматоAsJson*

> [!NOTE]
> * После создания, отображение может быть ссылкой на его имя в команды приема, вместо указания полного отображения как часть команды.
> * Действительные значения для _MappingKind:_ `CSV`, `JSON` `avro`, `parquet``orc`
> * Если отображение под тем же именем уже существует для таблицы:
>    * `.create`потерпит неудачу
>    * `.create-or-alter`изменит существующее отображение
 
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

**Вывод примера**

| Имя     | Вид | Сопоставление                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| отображение1 | CSV  | «Имя»:«Rownumber»,»,«DataType»:«int»,,»CsvDataType»:null,«Ordinal»:0,«ConstValue»: null», «Имя»:«rowguid»,«DataType»:«строка»», «CsvDataType»: null,«Ordinal»: |
