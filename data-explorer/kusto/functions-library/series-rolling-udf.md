---
title: series_rolling_udf () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_rolling_udf () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: bc4431e00b3947aa80404e4ba131dda7e813800e
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557955"
---
# <a name="series_rolling_udf"></a>series_rolling_udf ()


Функция `series_rolling_udf()` применяет последовательное агрегирование для ряда. Он принимает таблицу, содержащую несколько рядов (динамический числовой массив) и применяется для каждого ряда, последовательной статистической функции.

> [!NOTE]
> series_rolling_udf ():
>* Содержит встроенный язык Python и требует [включения подключаемого модуля Python ()](../query/pythonplugin.md#enable-the-plugin) в кластере.
>* — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke series_rolling_udf(`*y_series* `,` *y_rolling_series* `,` *n* `,` *аггр* `,` *aggr_params* `,` *Center*`)`

## <a name="arguments"></a>Аргументы

* *y_series*: имя столбца (входной таблицы), содержащего ряд для размещения.
* *y_rolling_series*: имя столбца для хранения последовательностей статистической обработки.
* *n*: ширина пошагового окна.
* *аггр*: имя используемой агрегатной функции. См. раздел [статистические функции](#aggregation-functions).
* *aggr_params*: необязательные параметры статистической функции.
* *Center*: Необязательное логическое значение, указывающее, является ли пошаговое окно одним из следующих вариантов:
    * применяется симметрично до и после текущей точки, либо 
    * применяется к текущей точке в обратном направлении. <br>
    По умолчанию *центр* имеет значение false для вычисления потоковой передачи данных.

## <a name="aggregation-functions"></a>Агрегатные функции

Эта функция поддерживает любую статистическую функцию из [NumPy](https://numpy.org/) или [SciPy. stats](https://docs.scipy.org/doc/scipy/reference/stats.html#module-scipy.stats) , которая вычисляет скалярное значение за пределами ряда. Следующий список не является исчерпывающим:

* [`sum`](https://numpy.org/doc/stable/reference/generated/numpy.sum.html#numpy.sum) 
* [`mean`](https://numpy.org/doc/stable/reference/generated/numpy.mean.html?highlight=mean#numpy.mean), [`min`](https://numpy.org/doc/stable/reference/generated/numpy.amin.html#numpy.amin)
* [`max`](https://numpy.org/doc/stable/reference/generated/numpy.amax.html)
* [`ptp (max-min)`](https://numpy.org/doc/stable/reference/generated/numpy.ptp.html)
* [`percentile`](https://numpy.org/doc/stable/reference/generated/numpy.percentile.html)
* [`median`](https://numpy.org/doc/stable/reference/generated/numpy.median.html)
* [`std`](https://numpy.org/doc/stable/reference/generated/numpy.std.html)
* [`var`](https://numpy.org/doc/stable/reference/generated/numpy.var.html)
* [`gmean` (среднее геометрическое значение)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.gmean.html)
* [`hmean` (среднее гармоническое)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.hmean.html)
* [`mode` (наиболее распространенное значение)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mode.html)
* [`moment` (n-<sup>й</sup> момент)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.moment.html)
* [`tmean` (среднее значение)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmean.html)
* [`tmin`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmin.html), [`tmax`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tmax.html)
* [тстд](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.tstd.html)
* [`iqr` (квантилей диапазон)](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.iqr.html) 

## <a name="usage"></a>Использование

* `series_rolling_udf()` — Это определяемая пользователем функция. Можно либо внедрить его код в запрос, либо установить в базе данных:
    * Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.
    * Для повторяющегося использования необходимо сохранить функцию с помощью [функции. Create](../management/create-function.md). <br>
        Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).
* `series_rolling_udf()` — Это [Табличная функция](../query/functions/user-defined-functions.md#tabular-function), применяемая с помощью [оператора Invoke](../query/invokeoperator.md).

# <a name="ad-hoc-usage"></a>[Нерегламентированное использование](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rolling_udf = (tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic=dynamic([null]), center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
;
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_udf('num', 'rolling_med', 9, 'median')
| render timechart
```

# <a name="persistent-usage"></a>[Постоянное использование](#tab/persistent)

* **Однократная установка**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Rolling window functions on a series")
series_rolling_udf(tbl:(*), y_series:string, y_rolling_series:string, n:int, aggr:string, aggr_params:dynamic, center:bool=true)
{
    let kwargs = pack('y_series', y_series, 'y_rolling_series', y_rolling_series, 'n', n, 'aggr', aggr, 'aggr_params', aggr_params, 'center', center);
    let code =
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_rolling_series = kargs["y_rolling_series"]\n'
        'n = kargs["n"]\n'
        'aggr = kargs["aggr"]\n'
        'aggr_params = kargs["aggr_params"]\n'
        'center = kargs["center"]\n'
        'result = df\n'
        'in_s = df[y_series]\n'
        'func = getattr(np, aggr, None)\n'
        'if not func:\n'
        '    import scipy.stats\n'
        '    func = getattr(scipy.stats, aggr)\n'
        'if func:\n'
        '    result[y_rolling_series] = list(pd.Series(in_s[i]).rolling(n, center=center, min_periods=1).apply(func, args=aggr_params).values for i in range(len(in_s)))\n'
        '\n';
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

* **Использование**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling median of 9 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_med = dynamic(null)
| invoke series_rolling_udf('num', 'rolling_med', 9, 'median', dynamic([null]))
| render timechart
```

---

:::image type="content" source="images/series-rolling-udf/rolling-median-9.png" alt-text="Диаграмма, показывающая скользящее медиана 9 элементов" border="false":::

## <a name="additional-examples"></a>Дополнительные примеры

В следующих примерах предполагается, что функция уже установлена:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling min, max & 75th percentile of 15 elements
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend rolling_min = dynamic(null), rolling_max = dynamic(null), rolling_pct = dynamic(null)
| invoke series_rolling_udf('num', 'rolling_min', 15, 'min', dynamic([null]))
| invoke series_rolling_udf('num', 'rolling_max', 15, 'max', dynamic([null]))
| invoke series_rolling_udf('num', 'rolling_pct', 15, 'percentile', dynamic([75]))
| render timechart
```

:::image type="content" source="images/series-rolling-udf/graph-rolling-15.png" alt-text="Диаграмма, показывающая скользящий минимум, максимальный & 75 процентиль из 15 элементов" border="false":::

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Calculate rolling trimmed mean
//
range x from 1 to 100 step 1
| extend y=iff(x % 13 == 0, 2.0, iff(x % 23 == 0, -2.0, rand()))
| summarize x=make_list(x), y=make_list(y)
| extend yr = dynamic(null)
| invoke series_rolling_udf('y', 'yr', 7, 'tmean', pack_array(pack_array(-2, 2), pack_array(false, false))) //  trimmed mean: ignoring values outside [-2,2] inclusive
| render linechart
```

:::image type="content" source="images/series-rolling-udf/rolling-trimmed-mean.png" alt-text="График с средним усеченным значением" border="false":::
