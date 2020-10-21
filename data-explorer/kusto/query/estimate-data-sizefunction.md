---
title: estimate_data_size () — обозреватель данных Azure
description: В этой статье описывается estimate_data_size () в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09d722b07b3ff49a294d4d8ce19a89563fc8f66e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253014"
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
