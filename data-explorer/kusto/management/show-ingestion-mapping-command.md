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
ms.openlocfilehash: 3c19426410046d7ff2357b4967333db8b039d9e6
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328999"
---
# <a name="show-ingestion-mappings"></a>. показывать сопоставления приема

Отображение сопоставлений приема (все или указанное по имени).

* `.show``table` *TableName* `ingestion` *маппингкинд*  `mappings`

* `.show``table` *TableName* `ingestion` *маппингкинд* `mapping` *MappingName*   

Показывать все сопоставления приема из всех типов сопоставлений:

* `.show` `table` *TableName* `ingestion`  `mapping`
 
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
