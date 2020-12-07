---
title: series_exp_smoothing_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_exp_smoothing_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: eabdb80852d2a81f996d3e722dda7df969a914be
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321817"
---
# <a name="series_exp_smoothing_fl"></a>series_exp_smoothing_fl()

Применяет базовый фильтр по экспоненциальному смягчению к ряду.

Функция `series_exp_smoothing_fl()` принимает выражение, содержащее динамический числовой массив, в качестве входных данных и применяет [основной экспоненциальный фильтр сглаживания](https://en.wikipedia.org/wiki/Exponential_smoothing#Basic_(simple)_exponential_smoothing_(Holt_linear)) .

> [!NOTE]
> Эта функция является [UDF (определяемой пользователем функцией)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`series_exp_smoothing_fl(`*y_series* `, [` *альфа-канал*`])`
  
## <a name="arguments"></a>Аргументы

* *y_series*: ячейка динамического массива числовых значений.
* *альфа*: необязательное вещественное значение в диапазоне [0-1], с указанием веса последней точки и веса предыдущих точек (то есть `1-alpha` ). Значение по умолчанию — 0,5.

## <a name="usage"></a>Использование

`series_exp_smoothing_fl()` — Это определяемая пользователем функция. Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_exp_smoothing_fl = (y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
;
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [`.create function`](../management/create-function.md) . Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Basic exponential smoothing for a series")
series_exp_smoothing_fl(y_series:dynamic, alpha:double=0.5)
{
    series_iir(y_series, pack_array(alpha), pack_array(1, alpha-1))
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 50 step 1
| extend y = x % 10
| summarize x = make_list(x), y = make_list(y)
| extend exp_smooth_y = series_exp_smoothing_fl(y, 0.4) 
| render linechart
```

---

:::image type="content" source="images/series-exp-smoothing-fl/exp-smoothing-demo.png" alt-text="Диаграмма, показывающая экспоненциальное сглаживание искусственного ряда" border="false":::
