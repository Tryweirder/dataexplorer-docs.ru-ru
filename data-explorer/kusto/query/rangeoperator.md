---
title: Оператор Range — Azure обозреватель данных
description: В этой статье описывается оператор Range в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5c736492745d47428b5919d9791aa6115aaf8566
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345901"
---
# <a name="range-operator"></a>Оператор range

Создает таблицу значений с одним столбцом.

Обратите внимание: входные данные конвейера отсутствуют. 

## <a name="syntax"></a>Синтаксис

`range`*ColumnName* `from` *Запуск* `to` *Завершение* `step` *шаг*

## <a name="arguments"></a>Аргументы

* *ColumnName*: имя одного столбца в выходной таблице.
* *Start*— наименьшее значение в выходных данных.
* *Завершение*: наибольшее значение, формируемое в выходных данных (или с максимальным значением, если шаг пошаговым *шагом* обойти это значение).
* *шаг*. разница между двумя последовательными значениями. 

Аргументы должны иметь числовые значения, значения даты или временного диапазона. Они не могут ссылаться на столбцы таблицы. (Если необходимо вычислить диапазон на основе входной таблицы, используйте функцию Range, возможно, с оператором MV-Expand.) 

## <a name="returns"></a>Результаты

Таблица с одним столбцом с именем *ColumnName*, значения которого *начинаются*, *начинаются* `+` *,*... до и до окончания *.*

## <a name="example"></a>Пример  

Таблица со значениями в полночь за последние семь дней. Функция bin (floor) позволяет сократить время до начала каждого дня.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range LastWeek from ago(7d) to now() step 1d
```

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|


Таблица с одним столбцом с именем `Steps`, типом `long` и значениями `1`, `4` и `7`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range Steps from 1 to 8 step 3
```

В следующем примере показано, как `range` оператор можно использовать для создания небольшой, нерегламентированной таблицы измерения, которая затем используется для отображения нулей, в которых исходные данные не имеют значений.

```kusto
range TIMESTAMP from ago(4h) to now() step 1m
| join kind=fullouter
  (Traces
      | where TIMESTAMP > ago(4h)
      | summarize Count=count() by bin(TIMESTAMP, 1m)
  ) on TIMESTAMP
| project Count=iff(isnull(Count), 0, Count), TIMESTAMP
| render timechart  
```
