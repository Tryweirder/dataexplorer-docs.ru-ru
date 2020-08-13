---
title: включить файл
description: включить файл
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 08/12/2020
ms.author: orspodek
ms.reviewer: tzgitlin
ms.custom: include file
ms.openlocfilehash: f63288ad25363f1d32184e45efb21b69a894da0c
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201720"
---
|**Свойство** | **Описание свойства**|
|---|---|
| `rawSizeBytes` | Размер необработанных (несжатых) данных. Для Avro/ORC/Parquet, который имеет размер до применения сжатия, характерного для формата. Укажите исходный размер данных, задав для этого свойства Размер несжатых данных в байтах.|
| `kustoTable` |  Имя существующей целевой таблицы. Переопределяет `Table` набор в `Data Connection` колонке. |
| `kustoDataFormat` |  Формат данных. Переопределяет `Data format` набор в `Data Connection` колонке. |
| `kustoIngestionMappingReference` |  Имя существующего сопоставления приема, которое будет использоваться. Переопределяет `Column mapping` набор в `Data Connection` колонке.|
| `kustoIgnoreFirstRecord` | Если задано значение `true` , Kusto игнорирует первую строку большого двоичного объекта. Для пропуска заголовков используйте данные в табличном формате (CSV, TSV или аналогичные). |
| `kustoExtentTags` | Строка, представляющая [теги](../kusto/management/extents-overview.md#extent-tagging) , которые будут присоединены к результирующему экстенту. |
| `kustoCreationTime` |  Переопределяет [$IngestionTime](../kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) для большого двоичного объекта в формате строки ISO 8601. Используется для заполнения. |
