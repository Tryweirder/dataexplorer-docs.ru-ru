---
title: series_fit_lowess_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_fit_lowess_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/29/2020
no-loc: LOWESS
ms.openlocfilehash: da384b1a907e4b524fc40f1c4133be5cf8e21c9c
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96469769"
---
# <a name="series_fit_lowess_fl"></a>series_fit_lowess_fl()

Функция `series_fit_lowess_fl()` применяет [ LOWESS регрессию](https://www.wikipedia.org/wiki/Local_regression) к ряду. Эта функция принимает таблицу с несколькими рядами (динамическими числовыми массивами) и создает *LOWESS кривую*, которая является сглаженной версией исходного ряда.

> [!NOTE]
> * `series_fit_lowess_fl()` — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md).
> * Эта функция содержит встроенный язык Python и требует [включения подключаемого модуля Python ()](../query/pythonplugin.md#enable-the-plugin) в кластере. Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke series_fit_lowess_fl(`*y_series* `,` *y_fit_series* `, [` *fit_size* `, ` *x_series* `,` *x_istime*]`)`

## <a name="arguments"></a>Аргументы

* *y_series*: имя столбца входной таблицы, содержащего [зависимую переменную](https://www.wikipedia.org/wiki/Dependent_and_independent_variables). Этот столбец представляет собой серию для размещения.
* *y_fit_series*: имя столбца для хранения рядов.
* *fit_size*. для каждой точки локальная Регрессия применяется к соответствующим *fit_size* ближайшим точкам. Этот параметр является необязательным, по умолчанию — *5*.
* *x_series*: имя столбца, содержащего [независимую переменную](https://www.wikipedia.org/wiki/Dependent_and_independent_variables), то есть ось x или Time. Этот параметр является необязательным и требуется только для [неравномерного пробельного ряда](https://www.wikipedia.org/wiki/Unevenly_spaced_time_series). Значение по умолчанию — пустая строка, так как x является избыточным для регрессии равномерно пробельных рядов.
* *x_istime*: Этот логический параметр необходим только в том случае, если указан аргумент *x_series* и является вектором даты и времени. Этот параметр является необязательным, по умолчанию — *false*.

## <a name="usage"></a>Использование

`series_fit_lowess_fl()` — Это [Табличная функция](../query/functions/user-defined-functions.md#tabular-function)определяемой пользователем функции, применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_lowess_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [`.create function`](../management/create-function.md) .  Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Установка в один момент времени

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fits a local polynomial using LOWESS method to a series")
series_fit_lowess_fl(tbl:(*), y_series:string, y_fit_series:string, fit_size:int=5, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_size', fit_size, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_size = kargs["fit_size"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'import statsmodels.api as sm\n'
        'def lowess_fit(ts_row, x_col, y_col, fsize):\n'
        '    y = ts_row[y_col]\n'
        '    fraction = fsize/len(y)\n'
        '    if x_col == "": # If there is no x column creates sequential range [1, len(y)]\n'
        '       x = np.arange(len(y)) + 1\n'
        '    else: # if x column exists check whether its a time column. If so, normalize it to the [1, len(y)] range, else take it as is.\n'
        '       if x_istime: \n'
        '           x = pd.to_numeric(pd.to_datetime(ts_row[x_col]))\n'
        '           x = x - x.min()\n'
        '           x = x / x.max()\n'
        '           x = x * (len(x) - 1) + 1\n'
        '       else:\n'
        '           x = ts_row[x_col]\n'
        '    lowess = sm.nonparametric.lowess\n'
        '    z = lowess(y, x, return_sorted=False, frac=fraction)\n'
        '    return list(z)\n'
        '\n'
        'result = df\n'
        'result[y_fit_series] = df.apply(lowess_fit, axis=1, args=(x_series, y_series, fit_size))\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Apply 9 points LOWESS regression on regular time series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null)
| invoke series_fit_lowess_fl('num', 'fnum', 9)
| render timechart
```

---

:::image type="content" source="images/series-fit-lowess-fl/lowess-regular-time-series.png" alt-text="Graph showing nine points :::No-Loc (ЛОВЕСС)::. по размеру регулярного временного ряда "border =" false ":::

## <a name="examples"></a>Примеры

В следующих примерах предполагается, что функция уже установлена:

### <a name="test-irregular-time-series"></a>Проверить неравномерные временные ряды

В следующем примере проверяются нестандартные временные ряды (неравномерное пространство)
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-1d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create irregular time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null)
    | invoke series_fit_lowess_fl('num', 'fnum', 9, 'TimeStamp', True)
    | render timechart 
    ```
    
    :::image type="content" source="images/series-fit-lowess-fl/lowess-irregular-time-series.png" alt-text="Graph showing nine points LOWESS fit to an irregular time series" border="false":::

Сравнение LOWESS и степень полинома

Следующий пример содержит пятое значение полинома порядка с шумом по осям x и y. См. раздел сравнение LOWESS и степень полинома. 

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | summarize x=make_list(x), y=make_list(y)
    | extend y_lowess = dynamic(null)
    | invoke series_fit_lowess_fl('y', 'y_lowess', 15, 'x')
    | extend series_fit_poly(y, x, 5)
    | project x, y, y_lowess, y_polynomial=series_fit_poly_y_poly_fit
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-lowess-fl/lowess-vs-poly-fifth-order-noise.png" alt-text="Graphs of LOWESS vs polynomial fit for a fifth order polynomial with noise on x & y axes":::
