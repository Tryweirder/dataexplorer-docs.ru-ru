---
title: Оператор DISTINCT в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор DISTINCT в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 233d3fdb0e25720b860a0c11515daec7c597dadd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348349"
---
# <a name="distinct-operator"></a>Оператор distinct

Создает таблицу с различным сочетанием указанных столбцов входной таблицы. 

```kusto
T | distinct Column1, Column2
```

Создает таблицу с различным сочетанием всех столбцов во входной таблице.

```kusto
T | distinct *
```

## <a name="example"></a>Пример

Показывает отдельное сочетание фруктов и цены.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Distinct":::

**Примечания**

* В отличие от `summarize by ...` , `distinct` оператор поддерживает предоставление звездочки ( `*` ) в качестве ключа группы, что упрощает его использование для широких таблиц.
* Если ключи group by имеют большое количество элементов, `summarize by ...` может оказаться полезным использование с [стратегией случайного](shufflequery.md) использования.