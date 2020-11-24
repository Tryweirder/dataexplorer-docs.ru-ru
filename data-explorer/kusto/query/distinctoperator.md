---
title: Оператор DISTINCT в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается оператор DISTINCT в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513171"
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

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Две таблицы. У одной из них есть поставщики, типы фруктов и цены, которые повторяются с некоторыми комбинациями фруктов. Во второй таблице перечислены только уникальные сочетания.":::

**Примечания**

* В отличие от `summarize by ...` , `distinct` оператор поддерживает предоставление звездочки ( `*` ) в качестве ключа группы, что упрощает его использование для широких таблиц.
* Если ключи group by имеют большое количество элементов, `summarize by ...` может оказаться полезным использование с [стратегией случайного](shufflequery.md) использования.