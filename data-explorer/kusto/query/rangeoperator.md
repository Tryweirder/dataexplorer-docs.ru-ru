---
title: оператор диапазона - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается оператор диапазона в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7ec559a23e28568ce7abd8365cdc502ad05a9b0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510614"
---
# <a name="range-operator"></a>Оператор range

Создает таблицу значений с одним столбцом.

Обратите внимание: входные данные конвейера отсутствуют. 

**Синтаксис**

`range`*колонкаНаги* `from` *стоп-стоп* `to` *stop* `step` *шага*

**Аргументы**

* *ColumnName*: Имя одного столбца в таблице вывода.
* *начало*: Наименьшее значение в выходе.
* *стоп*: Наивысшее значение, генерируемое в выходе (или связанное с наивысшим значением, если *шаг* шагает по этому значению).
* *шаг*: Разница между двумя последовательными значениями. 

Аргументы должны иметь числовые значения, значения даты или временного диапазона. Они не могут ссылаться на столбцы таблицы. (Если вы хотите вычислить диапазон на основе таблицы ввода, используйте функцию диапазона, может быть, с оператором mv-expand.) 

**Возвращает**

Таблица с одной колонкой под названием *columnName*, значения которого *начинаются,* *начинают* `+` *шаг,*... до и до *остановки*.

**Пример**  

Таблица со значениями в полночь за последние семь дней. Функция bin (floor) позволяет сократить время до начала каждого дня.

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

```kusto
range Steps from 1 to 8 step 3
```

Следующий пример показывает, как `range` оператор может быть использован для создания небольшой, специальной, таблицы измерений, которая затем используется для введения нулей, где исходные данные не имеют значений.

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