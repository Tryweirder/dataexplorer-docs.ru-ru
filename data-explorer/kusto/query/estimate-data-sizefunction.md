---
title: estimate_data_size () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описаны estimate_data_size () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9664a7c9caf0506cdb7274eed5e143f89c82cebb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515731"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Возвращает предполагаемый размер данных в байтах выбранных столбцов табликов выражения.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**Синтаксис**

`estimate_data_size(*)`

`estimate_data_size(`*col1*`, `*col2*`, `...`)`

**Аргументы**

* *col1*, *col2*: Выбор ссылок столбца в исходном табулярном выражении, которые используются для оценки размера данных. Чтобы включить все столбцы, используйте `*` (звездочка) синтаксис.

**Возвращает**

* Расчетный размер данных в байтах рекордного размера. Оценка основана на типах данных и длинах значений.

**Примеры**

Расчет общего размера `estimated_data_size()`данных с использованием:

```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|Итог|
|---|
|180|