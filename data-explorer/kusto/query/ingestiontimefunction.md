---
title: ingestion_time () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ingestion_time () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ba4429639f7c4775eab34797b7d7ff04fa247482
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252278"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

Возвращает примерное время приема текущей записи.

Эта функция должна использоваться в контексте таблицы принимаемых данных, для которой была включена [Политика инжестионтиме](../management/ingestiontimepolicy.md) при приеме данных. В противном случае эта функция создает значения NULL.

::: zone-end

::: zone pivot="azuremonitor"

Извлекает `datetime` сведения о том, когда запись была принята и готова к выполнению запроса.

::: zone-end

> [!NOTE]
> Значение, возвращаемое этой функцией, является приблизительным, так как процесс приема может занять несколько минут, а несколько действий приема могут выполняться параллельно. Для обработки всех записей таблицы с гарантированно идентичными гарантиями используйте [курсоры базы данных](../management/databasecursor.md).

## <a name="syntax"></a>Синтаксис

`ingestion_time()`

## <a name="returns"></a>Результаты

`datetime`Значение, указывающее примерное время приема в таблицу.

## <a name="example"></a>Пример

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
