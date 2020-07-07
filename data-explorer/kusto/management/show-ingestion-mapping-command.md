---
title: . Отображение сопоставлений приема — Azure обозреватель данных
description: В этой статье описывается отображение сопоставлений приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 4a225c7d9cb1c5f99434c4595cbd798d020cfd9f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967440"
---
# <a name="show-ingestion-mapping"></a>.show ingestion mapping

Отображение сопоставлений приема (все или указанное по имени).

* `.show``table` *TableName* `ingestion` *маппингкинд*  `mappings`

* `.show``table` *TableName* `ingestion` *маппингкинд* `mapping` *MappingName*   

Показывать все сопоставления приема из всех типов сопоставлений:

* `.show``table` *TableName*`ingestion`  `mapping`
 
**Пример** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Пример выходных данных**

| Имя     | Вид | Сопоставление     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
