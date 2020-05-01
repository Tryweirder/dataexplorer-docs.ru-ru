---
title: . Отображение сопоставлений приема — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается отображение сопоставлений приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 711861a07896b7bdc4cf57bebbf1cdd0e01d064a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617176"
---
# <a name="show-ingestion-mappings"></a>. показывать сопоставления приема

Отображение сопоставлений приема (все или указанное по имени).

* `.show``table` *TableName* TableName `ingestion` *маппингкинд*  `mappings`

* `.show``table` *TableName* TableName `ingestion` *MappingKind* маппингкинд`mapping` *MappingName*   

Показывать все сопоставления приема из всех типов сопоставления:

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
| mapping1 | CSV  | [{"Name": "RowNumber", "Тип_данных": "int", "Ксвдататипе": NULL, "Ordinal": 0, "ConstValue": NULL}, {"имя": "ROWGUID", "DataType": "строка", "Ксвдататипе": NULL, "Ordinal": 1, "ConstValue": NULL}] |
