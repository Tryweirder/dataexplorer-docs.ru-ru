---
title: подключаемый модуль автокластера — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль автокластеров в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a7caa8935176b2d4d52bf8955262509bb2069dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248049"
---
# <a name="autocluster-plugin"></a>Подключаемый модуль autocluster

```kusto
T | evaluate autocluster()
```

`autocluster` находит общие шаблоны дискретных атрибутов (измерений) в данных. Затем он сокращает результаты исходного запроса, будь то 100 или 100 000 строк, до небольшого количества шаблонов. Подключаемый модуль был разработан для анализа сбоев (например, исключений или сбоев), но потенциально может работать с любым набором отфильтрованных данных.

> [!NOTE]
> `autocluster` в основном основывается на алгоритме Seed-Expand из следующего документа: [алгоритмы для интеллектуального анализа данных телеметрии с использованием дискретных атрибутов](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1). 


## <a name="syntax"></a>Синтаксис

`T | evaluate autocluster(`*аргументы*`)`

## <a name="returns"></a>Результаты

`autocluster`Подключаемый модуль возвращает набор шаблонов (обычно небольшой). Шаблоны записывают части данных с общими общими значениями для нескольких дискретных атрибутов. Каждый шаблон в результатах представлен строкой.

Первый столбец — это идентификатор сегмента. Следующие два столбца — это число и процент строк из исходного запроса, которые охватывает шаблон. Остальные столбцы берутся из исходного запроса. Их значение — это либо конкретное значение из столбца, либо подстановочное значение (по умолчанию NULL), означающее значения переменных.

Шаблоны не различаются, могут перекрываться и обычно не охватывают все исходные строки. Некоторые строки могут не охватываться ни одним из шаблонов.

> [!TIP]
> Используйте [WHERE](./whereoperator.md) и [Project](./projectoperator.md) во входном канале, чтобы уменьшить объем данных до того, что вас интересует.
>
> Если вы хотите узнать дополнительные сведения о конкретной строке, добавьте ее значения в фильтр `where` .

## <a name="arguments"></a>Аргументы 

> [!NOTE] 
> Все аргументы являются необязательными.

`T | evaluate autocluster(`[*SizeWeight*, *WeightColumn*, *NumSeeds*, *CustomWildcard*, *CustomWildcard*,...]`)`

Все аргументы необязательны, но они должны быть упорядочены, как показано выше. Чтобы указать, что должно использоваться значение по умолчанию, поставьте строковое значение тильды "~" (см. столбец "example" в таблице).

|Аргумент        | Тип, диапазон, по умолчанию              |Описание                | Пример                                        |
|----------------|-----------------------------------|---------------------------|------------------------------------------------|
| SizeWeight     | 0 < *double* < 1 [по умолчанию: 0,5]   | Предоставляет некоторый контроль над балансом между общими (высоким покрытием) и информативными (многими общими) значениями. Если увеличить значение, оно обычно сокращает количество шаблонов, и каждый шаблон занимает больше всего покрытия. Если вы уменьшите значение, оно обычно создает более конкретные шаблоны с более общими значениями и меньшее процентное покрытие. Непонятная формула представляет собой взвешенное среднее геометрическое значение между нормализованным универсальным показателем и информативным показателем с весовыми коэффициентами `SizeWeight` и `1-SizeWeight`                   | `T | evaluate autocluster(0.8)`                |
|WeightColumn    | *column_name*                     | Рассматривает каждую строку входных данных в соответствии с указанным весовым коэффициентом (по умолчанию весовой коэффициент каждой строки — 1). Аргумент должен быть именем числового столбца (например, int, Long, Real). Как правило, при использовании столбца с весовым коэффициентом следует учитывать выборку, группирование или агрегирование данных, внедренных в каждой строке.                                                                                                       | `T | evaluate autocluster('~', sample_Count)` | 
| NumSeeds        | *int* [по умолчанию: 25]              | Число начальных значений определяет число начальных локальных точек поиска для алгоритма. В некоторых случаях, в зависимости от структуры данных и увеличения числа начальных значений, число (или качество) результатов увеличивается через развернутое пространство поиска с медленным компромиссом запросов. Это значение уменьшает результаты в обоих направлениях, поэтому, если уменьшить его до пяти, это обеспечит незначительное повышение производительности. При увеличении до 50 не будет создавать дополнительные шаблоны.                                         | `T | evaluate autocluster('~', '~', 15)`       |
| CustomWildcard  | *"any_value_per_type"*           | Задает подстановочное значение для конкретного типа в таблице результатов. Он указывает, что текущий шаблон не имеет ограничений на этот столбец. Значение по умолчанию равно null, так как строка по умолчанию — пустая строка. Если значение по умолчанию является хорошим значением в данных, следует использовать другое значение с подстановочными знаками (например, `*` ).                                                                                                                | `T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))` |

## <a name="examples"></a>Примеры

### <a name="using-autocluster"></a>Использование автокластеров

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|Идентификатор сегмента|Счетчик|Процент|Состояние|EventType|Ущерб|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7||Град|NO
|1|512|8,7||Шквалистый ветер|YES
|2|898|15,3|Техас||

### <a name="using-custom-wildcards"></a>Использование настраиваемых подстановочных знаков

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|Идентификатор сегмента|Счетчик|Процент|Состояние|EventType|Ущерб|
|---|---|---|---|---|---|---|---|---|
|0|2278|38,7|\*|Град|NO
|1|512|8,7|\*|Шквалистый ветер|YES
|2|898|15,3|Техас|\*|\*

## <a name="see-also"></a>См. также раздел

* [basket](./basketplugin.md)
* [свести](./reduceoperator.md)
