---
title: quantize_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция quantize_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 38d353caf1e0352688ee91edfe7f1d2cef94d18a
ms.sourcegitcommit: 5aba5f694420ade57ef24b96699d9b026cdae582
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90998948"
---
# <a name="quantize_fl"></a>quantize_fl()


Функция `quantize_fl()` заячеекет столбцы метрик. Он куантизес столбцы метрик в метки категорий, основанные на алгоритме K-средних.

> [!NOTE]
> * `quantize_fl()` — Это [определяемая пользователем функция (UDF)](../query/functions/user-defined-functions.md).
> * Эта функция содержит встроенный язык Python и требует [включения подключаемого модуля Python ()](../query/pythonplugin.md#enable-the-plugin) в кластере. Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`T | invoke quantize_fl(`*num_bins* `,` *in_cols* `,` *out_cols* `,` *метки*`)`

## <a name="arguments"></a>Аргументы

* *num_bins*: требуемое число ячеек.
* *in_cols*: динамический массив, содержащий имена столбцов для квантуем.
* *out_cols*: динамический массив, содержащий имена соответствующих выходных столбцов для значений binned.
* *метки*: динамический массив, содержащий имена меток. Это необязательный параметр. Если *метки* не заданы, будут использоваться диапазоны ячеек.

## <a name="usage"></a>Использование

`quantize_fl()` определяемая пользователем [Табличная функция](../query/functions/user-defined-functions.md#tabular-function), применяемая с помощью [оператора Invoke](../query/invokeoperator.md). Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let quantize_fl=(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic=dynamic(null))
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
};
//
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke quantize_fl(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke quantize_fl(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [функцию. Create](../management/create-function.md). Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create function with (folder = "Packages\\ML", docstring = "Binning metric columns")
quantize_fl(tbl:(*), num_bins:int, in_cols:dynamic, out_cols:dynamic, labels:dynamic)
{
    let kwargs = pack('num_bins', num_bins, 'in_cols', in_cols, 'out_cols', out_cols, 'labels', labels);
    let code =
        '\n'
        'from sklearn.preprocessing import KBinsDiscretizer\n'
        '\n'
        'num_bins = kargs["num_bins"]\n'
        'in_cols = kargs["in_cols"]\n'
        'out_cols = kargs["out_cols"]\n'
        'labels = kargs["labels"]\n'
        '\n'
        'result = df\n'
        'binner = KBinsDiscretizer(n_bins=num_bins, encode="ordinal", strategy="kmeans")\n'
        'df_in = df[in_cols]\n'
        'bdata = binner.fit_transform(df_in)\n'
        'if labels is None:\n'
        '    for i in range(len(out_cols)):    # loop on each column and convert it to binned labels\n'
        '        ii = np.round(binner.bin_edges_[i], 3)\n'
        '        labels = [str(ii[j-1]) + \'-\' + str(ii[j]) for j in range(1, num_bins+1)]\n'
        '        result.loc[:,out_cols[i]] = np.take(labels, bdata[:, i].astype(int))\n'
        'else:\n'
        '    result[out_cols] = np.take(labels, bdata.astype(int))\n'
        ;
    tbl
    | evaluate python(typeof(*), code, kwargs)
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union 
(range x from 1 to 5 step 1),
(range x from 10 to 15 step 1),
(range x from 20 to 25 step 1)
| extend x_label='', x_bin=''
| invoke quantize_fl(3, pack_array('x'), pack_array('x_label'), pack_array('Low', 'Med', 'High'))
| invoke quantize_fl(3, pack_array('x'), pack_array('x_bin'), dynamic(null))
```

---

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
x    x_label    x_bin
1    Low        1.0-7.75
2    Low        1.0-7.75
3    Low        1.0-7.75
4    Low        1.0-7.75
5    Low        1.0-7.75
20   High       17.5-25.0
21   High       17.5-25.0
22   High       17.5-25.0
23   High       17.5-25.0
24   High       17.5-25.0
25   High       17.5-25.0
10   Med        7.75-17.5
11   Med        7.75-17.5
12   Med        7.75-17.5
13   Med        7.75-17.5
14   Med        7.75-17.5
15   Med        7.75-17.5
```
