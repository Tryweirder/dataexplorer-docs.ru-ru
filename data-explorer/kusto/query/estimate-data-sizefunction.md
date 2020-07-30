---
title: estimate_data_size () — обозреватель данных Azure
description: В этой статье описывается estimate_data_size () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 00438528f00c91a95fde9a77a7aa18a75002fce9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348247"
---
# <a name="estimate_data_size"></a>estimate_data_size()

Возвращает приблизительный размер данных в байтах выбранных столбцов табличного выражения.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

## <a name="syntax"></a>Синтаксис

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, ` *столбец Col2* `, ` ...`)`

## <a name="arguments"></a>Аргументы

* *col1*, *Col2*: выбор ссылок на столбцы в исходном табличном выражении, которые используются для оценки размера данных. Чтобы включить все столбцы, используйте `*` синтаксис (звездочка).

## <a name="returns"></a>Результаты

* Предполагаемый размер данных в байтах для размера записи. Оценка основана на типах данных и длине значений.

## <a name="examples"></a>Примеры

Вычисление общего размера данных с помощью `estimated_data_size()` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|Итог|
|---|
|180|
