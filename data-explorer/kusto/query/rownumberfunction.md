---
title: row_number () - Исследователь данных Azure Документы Майкрософт
description: Эта статья описывает row_number () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8cb01ed098d24632154215ddf06dc2ab1d72695
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510172"
---
# <a name="row_number"></a>row_number()

Возвращает индекс текущей строки в [набор серийного ряда.](./windowsfunctions.md#serialized-row-set)
Индекс строки начинается `1` по умолчанию в первом ряду `1` и приращается для каждой дополнительной строки.
В обязательном порядке индекс строки может `1`начинаться с иного значения, чем.
Кроме того, индекс строки может быть смаен в соответствии с некоторыми предусмотренными предикатами.

**Синтаксис**

`row_number``(` *Стартовыйиндекс* `,` и *перезагрузка*`)`

* *StartingIndex* — это постоянное выражение типа, `long` указывающее значение индекса строки для начала (или для перезапуска). Значение по умолчанию — `1`.
* *Перезагрузка* — это необязательный аргумент типа, `bool` указывающий, когда нумерация должна быть перезапущена к значению *StartingIndex.* Если это не предусмотрено, используется значение значения `false` по умолчанию.

**Возвращает**

Функция возвращает индекс строки текущей строки `long`в качестве значения типа.

**Примеры**

Следующий пример возвращает таблицу с двумя столбцов, первый`a`столбец ( ) с числами от `10` `1`до , и второй столбец ( )`rn`с числами от `1` до : `10`

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

Следующий пример похож на вышеуказанный,`rn`только вторая колонка ( ) начинается с: `7`

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

Последний пример показывает, как можно разгородить данные и прочислить строки на каждую раздел. Здесь мы разделим данные: `Airport`

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

Запуск этого запроса дает следующий результат:

Аэропорт  | Авиаперевозки  | Вылетов  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | Левая       | 1           | 2
SEA      | LY       | 0           | 3
Tlv      | LY       | 100         | 1
Tlv      | Левая       | 1           | 2