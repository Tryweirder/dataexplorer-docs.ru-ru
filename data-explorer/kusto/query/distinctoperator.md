---
title: Оператор distinct в Azure Data Explorer | Документация Майкрософт
description: В этой статье описан оператор distinct в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 86b8617698f3708edcebbc1c2c4bd1732054600f
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513171"
---
# <a name="distinct-operator"></a>Оператор distinct

Создает таблицу с уникальной комбинацией указанных столбцов входной таблицы. 

```kusto
T | distinct Column1, Column2
```

Создает таблицу с уникальной комбинацией всех столбцов входной таблицы.

```kusto
T | distinct *
```

## <a name="example"></a>Пример

Отображение уникальной комбинации столбцов фруктов и цены.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Две таблицы. В одной содержатся сведения о поставщиках, типах фруктов и ценах, а также некоторые повторяемые комбинации названий фруктов и цен. Во второй таблице перечислены только уникальные комбинации.":::

**Примечания**

* В отличие от `summarize by ...`, оператор `distinct` поддерживает указание звездочки (`*`) в качестве ключа группы. Это упрощает работу с широкими таблицами.
* Если ключи group by имеют большое количество элементов, рекомендуем воспользоваться `summarize by ...` со [стратегией группирования в случайном порядке](shufflequery.md).