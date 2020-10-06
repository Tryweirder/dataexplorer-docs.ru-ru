---
title: series_fit_poly_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_fit_poly_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: eff9a5cd8ed2d9ed7e518be9aade9ecf2aded7bf
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91765464"
---
# <a name="series_fit_poly_fl"></a>series_fit_poly_fl()

Функция `series_fit_poly_fl()` применяет регрессионную регрессию для ряда. Эта функция принимает таблицу, содержащую несколько рядов (динамические числовые массивы), и формирует наилучшее значение степени соответствия высокого порядка для каждого ряда, используя [регрессию полинома](https://en.wikipedia.org/wiki/Polynomial_regression). Эта функция возвращает как коэффициенты полинома, так и интерполяцию на основе диапазона ряда.

> [!NOTE]
> Используйте собственную функцию [series_fit_poly ()](../query/series-fit-poly-function.md). Приведенная ниже функция предназначена только для справки.


> [!NOTE]
> * `series_fit_poly_fl()` — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md).
> * Эта функция содержит встроенный язык Python и требует [включения подключаемого модуля Python ()](../query/pythonplugin.md#enable-the-plugin) в кластере. Дополнительные сведения см. в разделе [Использование](#usage).
> * Для линейной регрессии для ряда с четными интервалами, созданной [оператором make-Series](../query/make-seriesoperator.md), используется собственная функция [series_fit_line ()](../query/series-fit-linefunction.md).

## <a name="syntax"></a>Синтаксис

`T | invoke series_fit_poly_fl(`*y_series* `,` *y_fit_series* `,` *fit_coeff* `,` *степень* `, [` *x_series* `,` *x_istime*]`)`
  
## <a name="arguments"></a>Аргументы

* *y_series*: имя столбца входной таблицы, содержащего [зависимую переменную](https://en.wikipedia.org/wiki/Dependent_and_independent_variables). Это значит, что ряды нужно разместить.
* *y_fit_series*: имя столбца, в котором хранятся наиболее подходящие ряды.
* *fit_coeff*: имя столбца, в котором хранятся наиболее подходящие коэффициенты полинома.
* *степень*: требуемый порядок полинома для размещения. Например, 1 для линейной регрессии, 2 для квадратичной регрессии и т. д.
* *x_series*: имя столбца, содержащего [независимую переменную](https://en.wikipedia.org/wiki/Dependent_and_independent_variables), то есть ось x или Time. Этот параметр является необязательным и требуется только для [неравномерного пробельного ряда](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series). Значение по умолчанию — пустая строка, так как x является избыточным для регрессии равномерно пробельных рядов.
* *x_istime*: Этот логический параметр является необязательным. Этот параметр необходим только в том случае, если указан аргумент *x_series* и является вектором даты и времени.

## <a name="usage"></a>Использование

`series_fit_poly_fl()` — Это [Табличная функция](../query/functions/user-defined-functions.md#tabular-function)определяемой пользователем функции, применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_fit_poly_fl=(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=False)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
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
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
};
//
// Fit fifth order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [функцию. Create](../management/create-function.md).  Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Установка в один момент времени

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Fit a polynomial of a specified degree to a series")
series_fit_poly_fl(tbl:(*), y_series:string, y_fit_series:string, fit_coeff:string, degree:int, x_series:string='', x_istime:bool=false)
{
    let kwargs = pack('y_series', y_series, 'y_fit_series', y_fit_series, 'fit_coeff', fit_coeff, 'degree', degree, 'x_series', x_series, 'x_istime', x_istime);
    let code=
        '\n'
        'y_series = kargs["y_series"]\n'
        'y_fit_series = kargs["y_fit_series"]\n'
        'fit_coeff = kargs["fit_coeff"]\n'
        'degree = kargs["degree"]\n'
        'x_series = kargs["x_series"]\n'
        'x_istime = kargs["x_istime"]\n'
        '\n'
        'def fit(ts_row, x_col, y_col, deg):\n'
        '    y = ts_row[y_col]\n'
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
        '    coeff = np.polyfit(x, y, deg)\n'
        '    p = np.poly1d(coeff)\n'
        '    z = p(x)\n'
        '    return z, coeff\n'
        '\n'
        'result = df\n'
        'if len(df):\n'
        '   result[[y_fit_series, fit_coeff]] = df.apply(fit, axis=1, args=(x_series, y_series, degree,), result_type="expand")\n'
    ;
    tbl
     | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
// Fit fifth order polynomial to a regular (evenly spaced) time series, created with make-series
//
let max_t = datetime(2016-09-03);
demo_make_series1
| make-series num=count() on TimeStamp from max_t-1d to max_t step 5m by OsVer
| extend fnum = dynamic(null), coeff=dynamic(null), fnum1 = dynamic(null), coeff1=dynamic(null)
| invoke series_fit_poly_fl('num', 'fnum', 'coeff', 5)
| render timechart with(ycolumns=num, fnum)
```

---

:::image type="content" source="images/series-fit-poly-fl/usage-example.png" alt-text="Диаграмма, показывающая пятое значение полинома в соответствии с регулярным временным набором" border="false":::

## <a name="additional-examples"></a>Дополнительные примеры

В следующих примерах предполагается, что функция уже установлена:

1. Проверка неравномерного (неравномерного) временного ряда
    
    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    let max_t = datetime(2016-09-03);
    demo_make_series1
    | where TimeStamp between ((max_t-2d)..max_t)
    | summarize num=count() by bin(TimeStamp, 5m), OsVer
    | order by TimeStamp asc
    | where hourofday(TimeStamp) % 6 != 0   //  delete every 6th hour to create unevenly spaced time series
    | summarize TimeStamp=make_list(TimeStamp), num=make_list(num) by OsVer
    | extend fnum = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('num', 'fnum', 'coeff', 8, 'TimeStamp', True)
    | render timechart with(ycolumns=num, fnum)
    ```
    
    :::image type="content" source="images/series-fit-poly-fl/irregular-time-series.png" alt-text="Диаграмма, показывающая пятое значение полинома в соответствии с регулярным временным набором" border="false":::

1. Пятый порядок полинома с шумом по осям x & y

    <!-- csl: https://help.kusto.windows.net:443/Samples -->
    ```kusto
    range x from 1 to 200 step 1
    | project x = rand()*5 - 2.3
    | extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
    | extend y = y + (rand() - 0.5)*0.5*y
    | summarize x=make_list(x), y=make_list(y)
    | extend y_fit = dynamic(null), coeff=dynamic(null)
    | invoke series_fit_poly_fl('y', 'y_fit', 'coeff', 5, 'x')
    |fork (project-away coeff) (project coeff | mv-expand coeff)
    | render linechart
    ```
        
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise.png" alt-text="Диаграмма, показывающая пятое значение полинома в соответствии с регулярным временным набором":::
       
    :::image type="content" source="images/series-fit-poly-fl/fifth-order-noise-table.png" alt-text="Диаграмма, показывающая пятое значение полинома в соответствии с регулярным временным набором" border="false":::
