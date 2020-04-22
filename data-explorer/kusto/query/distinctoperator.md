---
title: различный оператор - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается отдельный оператор в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4287ca48d3fb5006e67a9266ea05287a7d8a06f6
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030380"
---
# <a name="distinct-operator"></a>Оператор distinct

Производит таблицу с отчетливым сочетанием предоставленных столбцов таблицы ввода. 

```kusto
T | distinct Column1, Column2
```

Производит таблицу с отчетливым сочетанием всех столбцов в таблице ввода.

```kusto
T | distinct *
```

**Пример**

Показывает четкое сочетание фруктов и цены.

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="Различных":::

**Примечания**

* В `summarize by ...`отличие `distinct` от, оператор поддерживает`*`предоставление звездочки () в качестве ключа группы, что упрощает использование для широких таблиц.
* Если группа по клавишам имеют `summarize by ...` высокие кардинальности, использование [со стратегией перетасовки](shufflequery.md) может быть полезным.