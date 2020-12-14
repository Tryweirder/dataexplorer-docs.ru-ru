---
title: series_metric_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_metric_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 12/13/2020
ms.openlocfilehash: 2af82906be2cdbffdc69646a3050376e0a1af867
ms.sourcegitcommit: fcaf3056db2481f0e3f4c2324c4ac956a4afef38
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97389194"
---
# <a name="series_metric_fl"></a>series_metric_fl ()


`series_metric_fl()`Функция выбирает и извлекает временные ряды метрик, принимаемые в Azure обозреватель данных с помощью системы мониторинга [Prometheus](https://prometheus.io/) . Эта функция предполагает, что данные, хранящиеся в обозреватель данных Azure, структурированы после [модели данных Prometheus](https://prometheus.io/docs/concepts/data_model/). В частности, каждая запись содержит:
 * TIMESTAMP 
 * имя метрики 
 * значение метрики 
 * набор переменных меток ( `key:value` пар)
 
 Prometheus определяет временные ряды по имени метрики и отдельному набору меток. Наборы временных рядов можно получить с помощью [языка запросов Prometheus (промкл)](https://prometheus.io/docs/prometheus/latest/querying/basics/) , указав имя метрики и селектор временных рядов (набор меток).

> [!NOTE]
> `series_metric_fl()` — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke series_metric_fl(`*timestamp_col* `,` *name_col* `,` *labels_col* `,` *value_col* `,` *metric_name* `,` *labels_selector* `,` *лукбакк* `,` *смещение*`)`

## <a name="arguments"></a>Аргументы

* *timestamp_col*: имя столбца, содержащего метку времени.
* *name_col*: имя столбца, содержащего имя метрики.
* *labels_col*: имя столбца, содержащего словарь меток.
* *value_col*: имя столбца, содержащего значение метрики.
* *metric_name*: метрика временных рядов для извлечения.
* *labels_selector*: строка селектора временных рядов, [аналогичная промкл](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-series-selectors). Это строка, содержащая список `key:value` пар, например `'key1:val1,key2:val2'` . Этот параметр является необязательным. по умолчанию используется пустая строка, что означает отсутствие фильтрации. Обратите внимание, что регулярные выражения не поддерживаются. 
* *лукбакк*: вектор диапазона для извлечения, [аналогичный промкл](https://prometheus.io/docs/prometheus/latest/querying/basics/#range-vector-selectors). Этот параметр является необязательным, по умолчанию — 10 минут.
* *смещение*: обратное смещение относительно текущего времени для получения, [аналогично промкл](https://prometheus.io/docs/prometheus/latest/querying/basics/#offset-modifier). Данные извлекаются из *назад (offset) — лукбакк* to *назад (offset)*. Этот параметр является необязательным, по умолчанию — 0. Это означает, что данные извлекаются до Now ().

## <a name="usage"></a>Использование

`series_metric_fl()` определяемая пользователем [Табличная функция](../query/functions/user-defined-functions.md#tabular-function), применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_metric_fl=(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
;
//
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [`.create function`](../management/create-function.md) . Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\Series", docstring = "Selecting & retrieving metrics like PromQL")
series_metric_fl(metrics_tbl:(*), timestamp_col:string, name_col:string, labels_col:string, value_col:string, metric_name:string, labels_selector:string='', lookback:timespan=timespan(10m), offset:timespan=timespan(0))
{
    let selector_d=iff(labels_selector == '', dynamic(['']), split(strcat('"', replace('([:,])','"\\1"', labels_selector), '"'), ','));
    let etime = ago(offset);
    let stime = etime - lookback;
    metrics_tbl
    | extend timestamp = column_ifexists(timestamp_col, datetime(null)), name = column_ifexists(name_col, ''), labels = column_ifexists(labels_col, dynamic(null)), value = column_ifexists(value_col, 0)
    | extend labels = dynamic_to_json(labels)       //  convert to string and sort by key
    | where name == metric_name and timestamp between(stime..etime)
    | project timestamp, value, name, labels
    | order by timestamp asc
    | summarize timestamp = make_list(timestamp), value=make_list(value) by name, labels
    //  KQL has native has_any(), but no native has_all(), the lines below implement has_all()
    | mv-apply x = selector_d to typeof(string) on (
      summarize countif(labels has x)
      | where countif_ == array_length(selector_d)
    )
    | project-away countif_
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_prometheus
| invoke series_metric_fl('TimeStamp', 'Name', 'Labels', 'Val', 'writes', 'disk:sda1,host:aks-agentpool-88086459-vmss000001', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels)
```

---

:::image type="content" source="images/series-metric-fl/disk-write-metric-10m.png" alt-text="Диаграмма, показывающая метрику записи на диск более 10 минут" border="false":::

## <a name="example"></a>Пример

В следующем примере не указывается селектор, поэтому выбираются все метрики записи. В этом примере предполагается, что функция уже установлена и использует альтернативный синтаксис прямого вызова, указывая входную таблицу в качестве первого параметра:
    
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
series_metric_fl(demo_prometheus, 'TimeStamp', 'Name', 'Labels', 'Val', 'writes', offset=now()-datetime(2020-12-08 00:00))
| render timechart with(series=labels, ysplit=axes)
```
    
:::image type="content" source="images/series-metric-fl/all-disks-write-metric-10m.png" alt-text="Диаграмма, показывающая метрику записи диска для всех дисков за 10 минут" border="false":::
