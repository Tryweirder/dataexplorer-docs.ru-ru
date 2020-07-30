---
title: ingestion_time () — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается ingestion_time () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f40a592521082667815fe3ff38843a2376bda0aa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347414"
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
