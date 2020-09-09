---
title: series_moving_avg_lf () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_moving_avg_lf () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 918881858a978fd10dc7089b408b0ad626f5c446
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614953"
---
# <a name="series_moving_avg_lf"></a>series_moving_avg_lf ()

Применяет фильтр скользящего среднего к ряду.

Функция `series_moving_avg_lf()` принимает выражение, содержащее динамический числовой массив в качестве входных данных, и применяет [простой фильтр скользящего среднего](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) .

> [!NOTE]
> Эта функция является [UDF (определяемой пользователем функцией)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`series_moving_avg_lf(`*y_series* `,` *n* `, [` по *центру*`])`
  
## <a name="arguments"></a>Аргументы

* *y_series*: ячейка динамического массива числовых значений.
* *n*: ширина фильтра скользящего среднего.
* *Center*: Необязательное логическое значение, указывающее, является ли скользящее среднее одним из следующих вариантов:
    * применяет симметричное применение к окну до и после текущей точки или 
    * применяется к окну из текущей точки в обратном направлении. <br>
    По умолчанию *центр* имеет значение false.

## <a name="usage"></a>Использование

`series_moving_avg_lf()` — Это определяемая пользователем функция. Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_udf = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_lf(num, 5, True)
| render timechart 
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [функцию. Create](../management/create-function.md). Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_lf(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_lf(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-lf/moving-average-five-bins.png" alt-text="Диаграмма, показывающая скользящее среднее из 5 ячеек" border="false":::
