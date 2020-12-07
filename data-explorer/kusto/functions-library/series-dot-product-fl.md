---
title: series_dot_product_fl () — обозреватель данных Azure
description: В этой статье описывается определяемая пользователем функция series_dot_product_fl () в Azure обозреватель данных.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 9065c6b86807ad27c588ebffe3334c450a1addcb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321851"
---
# <a name="series_dot_product_fl"></a>series_dot_product_fl()

Вычисляет скалярное произведение двух числовых векторов.

Функция `series_dot_product_fl()` принимает выражение, содержащее два динамических числовых массива, в качестве входных данных и вычисляет свою [точку произведения](https://en.wikipedia.org/wiki/Dot_product).

> [!NOTE]
> Эта функция является [UDF (определяемой пользователем функцией)](../query/functions/user-defined-functions.md). Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="syntax"></a>Синтаксис

`series_dot_product_fl(`*vec1* `,` *vec2*`)`
  
## <a name="arguments"></a>Аргументы

* *vec1*: ячейка динамического массива числовых значений.
* *vec2*: ячейка динамического массива числовых значений, длина которой равна *vec1*.

## <a name="usage"></a>Использование

`series_dot_product_fl()` — Это определяемая пользователем функция. Можно либо внедрить его код в запрос, либо установить в базе данных. Существует два варианта использования: прямое и постоянное использование. Примеры см. на следующих вкладках.

# <a name="ad-hoc"></a>[Прямое соединение](#tab/adhoc)

Для нерегламентированного использования внедрите его код с помощью [инструкции Let](../query/letstatement.md). Никаких разрешений не требуется.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_dot_product_fl=(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
};
//
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

Для постоянного использования используйте [`.create function`](../management/create-function.md) . Для создания функции требуется [разрешение пользователя базы данных](../management/access-control/role-based-authorization.md).

### <a name="one-time-installation"></a>Однократная установка

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate the dot product of 2 numerical arrays")
series_dot_product_fl(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
}
```

### <a name="usage"></a>Использование

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

---

:::image type="content" source="images/series-dot-product-fl/dot-product-result.png" alt-text="Таблица, показывающая результат 2 векторного произведения с помощью определяемой пользователем функции series_dot_product_fl в Azure обозреватель данных" border="false":::
