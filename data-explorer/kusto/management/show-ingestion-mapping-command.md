---
title: .show повышающие сярпинги - Azure Data Explorer Документы Майкрософт
description: В этой статье описаны отображения .show ingestion в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 91990fe40664ae89d69357812b0def2c7288eb7d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519828"
---
# <a name="show-ingestion-mappings"></a>.show ingestion отображение

Отображение карт опродающихся данных (все или тот, указанный по имени).

* `.show``table` *ТаблицаИмя* `ingestion` *КартпингКинд*  `mappings`

* `.show``table` *TableName* `ingestion` *КартированиеKind* `mapping` *MappingName*   

Показать все отображения приема всех видов отображения:

* `.show``table` *Название таблицы*`ingestion`  `mapping`
 
**Пример** 
 
```
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Вывод примера**

| Имя     | Вид | Сопоставление     |
|----------|------|-------------|
| отображение1 | CSV  | «Имя»:«Rownumber»,»,«DataType»:«int»,,»CsvDataType»:null,«Ordinal»:0,«ConstValue»: null», «Имя»:«rowguid»,«DataType»:«строка»», «CsvDataType»: null,«Ordinal»: |