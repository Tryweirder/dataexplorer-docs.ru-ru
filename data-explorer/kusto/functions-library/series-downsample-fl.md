---
title: series_downsample_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_downsample_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2020
ms.openlocfilehash: 172d866a84e4718e3d95e8fa646187e79bec65f7
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321868"
---
# <a name="series_downsample_fl"></a>series_downsample_fl()


Функция `series_downsample_fl()` [довнсамплес временной ряд по целому коэффициенту](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor). Эта функция принимает таблицу, содержащую несколько временных рядов (динамический числовой массив), и довнсамплес каждый ряд. Выходные данные содержат как ряд более крупных, так и соответствующие массивы времени. Чтобы избежать [присвоения псевдонимов](https://en.wikipedia.org/wiki/Aliasing), функция применяет простой [Фильтр низкого прохода](https://en.wikipedia.org/wiki/Low-pass_filter) к каждому ряду перед подвыборкой.

> [!NOTE]
> Эта функция является [UDF (определяемой пользователем функцией)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke series_downsample_fl(`*t_col* `,` *y_col* `,` *ds_t_col* `,` *ds_y_col* `,` *sampling_factor*`)`

## <a name="arguments"></a>Аргументы

* *t_col*: имя столбца (входной таблицы), содержащего ось времени для понизить его.
* *y_col*: имя столбца (входной таблицы), содержащего ряд для понижения.
* *ds_t_col*: имя столбца для хранения оси времени downsampled для каждого ряда.
* *ds_y_col*: имя столбца для хранения ряда выборки.
* *sampling_factor*: целое число, определяющее требуемую выборку.

## <a name="usage"></a>Использование

`series_downsample_fl()` определяемая пользователем [Табличная функция](../query/functions/user-defined-functions.md#tabular-function), применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_downsample_fl=(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
;
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [`.create function`](../management/create-function.md) . Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Downsampling a series by an integer factor")
series_downsample_fl(tbl:(*), t_col:string, y_col:string, ds_t_col:string, ds_y_col:string, sampling_factor:int)
{
    tbl
    | extend _t_ = column_ifexists(t_col, dynamic(0)), _y_ = column_ifexists(y_col, dynamic(0))
    | extend _y_ = series_fir(_y_, repeat(1, sampling_factor), true, true)    //  apply a simple low pass filter before sub-sampling
    | mv-apply _t_ to typeof(DateTime), _y_ to typeof(double) on
    (extend rid=row_number()
    | where rid % sampling_factor == (sampling_factor/2+1)                    //  sub-sampling
    | summarize _t_ = make_list(_t_), _y_ = make_list(_y_))
    | extend cols = pack(ds_t_col, _t_, ds_y_col, _y_)
    | project-away _t_, _y_
    | evaluate bag_unpack(cols)
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| invoke series_downsample_fl('TimeStamp', 'num', 'coarse_TimeStamp', 'coarse_num', 4)
| render timechart with(xcolumn=coarse_TimeStamp, ycolumns=coarse_num)
```

---

Временной ряд, downsampled на 4: :::image type="content" source="images/series-downsample-fl/downsampling-demo.png" alt-text="граф, демонстрирующий понижение временных рядов" border="false":::

Для справки ниже приведен исходный временный ряд (до понижения разрешения):
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| render timechart with(xcolumn=TimeStamp, ycolumns=num)
```

:::image type="content" source="images/series-downsample-fl/original-time-series.png" alt-text="Диаграмма, показывающая исходные временные ряды, до понижения" border="false":::

