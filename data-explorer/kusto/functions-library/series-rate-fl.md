---
title: series_rate_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_rate_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 9d34fa3d880b4888cd7f43913644e9cba8b4ca9d
ms.sourcegitcommit: 335e05864e18616c10881db4ef232b9cda285d6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97596844"
---
# <a name="series_rate_fl"></a>series_rate_fl ()


Функция `series_rate_fl()` вычисляет среднюю скорость увеличения метрики в секунду. Его логика соответствует функции Промкл [Rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) . Его следует использовать для временных рядов метрик счетчиков, принимаемых в Azure обозреватель данных системой мониторинга [Prometheus](https://prometheus.io/) и получаемых [series_metric_fl ()](series-metric-fl.md).

> [!NOTE]
>`series_rate_fl()` — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke series_rate_fl(`*n_bins*`)`

`T` — Это таблица, возвращаемая из [series_metric_fl ()](series-metric-fl.md). Его схема включает `(timestamp:dynamic, name:string, labels:string, value:dynamic)` .

## <a name="arguments"></a>Аргументы

* *n_bins*: количество ячеек для указания разрыва между извлеченными значениями метрики для вычисления ставки. Функция вычисляет разницу между текущим образцом и одной *n_bins* ранее и делит ее на разность соответствующих меток времени в секундах. Этот параметр является необязательным и имеет значение по умолчанию, равное одной ячейке. Параметры по умолчанию вычисляют [ирате ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate), функцию промкл мгновенной скорости.
* *fix_reset*: логический флаг, определяющий, следует ли проверять Сброс счетчиков и исправлять его, например функцию промкл [Rate ()](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate) . Этот параметр является необязательным и имеет значение по умолчанию `true` . Задайте для параметра значение `false` , чтобы сохранить избыточный анализ в случае, если не нужно проверять наличие сбросов.

## <a name="usage"></a>Использование

`series_rate_fl()` определяемая пользователем [Табличная функция](../query/functions/user-defined-functions.md#tabular-function), применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

> [!NOTE]
> [series_metric_fl ()](series-metric-fl.md) используется как часть функции и должна быть установлена или внедрена.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_rate_fl=(tbl:(timestamp:dynamic, value:dynamic), n_bins:int=1, fix_reset:bool=true)
{
    tbl
    | where fix_reset                                                   //  Prometheus counters can only go up
    | mv-apply value to typeof(double) on   
    ( extend correction = iff(value < prev(value), prev(value), 0.0)    // if the value decreases we assume it was reset to 0, so add last value
    | extend cum_correction = row_cumsum(correction)
    | extend corrected_value = value + cum_correction
    | summarize value = make_list(corrected_value))
    | union (tbl | where not(fix_reset))
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend rate = series_divide(dv, dt)
    | project-away dt, dv, timestampS, value, valueS
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [`.create function`](../management/create-function.md) . Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Simulate PromQL rate()")
series_rate_fl(tbl:(timestamp:dynamic, value:dynamic), n_bins:int=1, fix_reset:bool=true)
{
    tbl
    | where fix_reset                                                   //  Prometheus counters can only go up
    | mv-apply value to typeof(double) on   
    ( extend correction = iff(value < prev(value), prev(value), 0.0)    // if the value decreases we assume it was reset to 0, so add last value
    | extend cum_correction = row_cumsum(correction)
    | extend corrected_value = value + cum_correction
    | summarize value = make_list(corrected_value))
    | union (tbl | where not(fix_reset))
    | extend timestampS = array_shift_right(timestamp, n_bins), valueS = array_shift_right(value, n_bins)
    | extend dt = series_subtract(timestamp, timestampS)
    | extend dt = series_divide(dt, 1e7)                              //  converts from ticks to seconds
    | extend dv = series_subtract(value, valueS)
    | extend rate = series_divide(dv, dt)
    | project-away dt, dv, timestampS, value, valueS
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| invoke series_rate_fl(2)
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-rate-fl/all-disks-write-rate-2-bins.png" alt-text="Диаграмма, показывающая частоту записи на диск для всех дисков в секунду" border="false":::

## <a name="example"></a>Пример

В следующем примере выбирается основной диск двух узлов, и предполагается, что функция уже установлена. В этом примере используется альтернативный синтаксис прямого вызова, указывающий входную таблицу в качестве первого параметра:
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_rate_fl(series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1', lookback=2h, offset=now()-datetime(2020-12-08 00:00)), n_bins=10)
| render timechart with(series=labels)
```
    
:::image type="content" source="images/series-rate-fl/main-disks-write-rate-10-bins.png" alt-text="График, отображающий частоту записи основного диска в секунду за последние два часа с интервалом в 10 ячеек" border="false":::
