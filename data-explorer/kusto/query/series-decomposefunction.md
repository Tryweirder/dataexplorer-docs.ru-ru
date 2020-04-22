---
title: series_decompose () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_decompose () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 375d63dd050840cd884fca0198511e71ac46f170
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663606"
---
# <a name="series_decompose"></a>series_decompose()

Применяет трансформацию разложения в серии.  

Принимает выражение, содержащее ряд (динамический числовой массив) в качестве ввода и разлагает его на сезонные, трендовые и остаточные компоненты.
 
**Синтаксис**

`series_decompose(`*Серия* `[,` *Сезонность* `,` *Тенденция* `,` *Test_points* `,` *Seasonality_threshold*`])`

**Аргументы**

* *Серия*: Динамическая ячейка массива, которая представляет собой массив численных значений, как правило, в результате выход [из серии или](make-seriesoperator.md) [make_list](makelist-aggfunction.md) операторов
* *Сезонность*: несколько контролирующих сезонный анализ, содержащий либо
    * -1: автоматическое обнаружение сезонности с использованием [series_periods_detect](series-periods-detectfunction.md) (по умолчанию).
    * период: положительный целый период с указанием ожидаемого периода в количестве бункеров. Например, если серия находится в 1h бункеров, еженедельный период составляет 168 бункеров.
    * 0: нет сезонности (пропустить извлечение этого компонента).    
* *Тренд*: Строка, контролируя анализ тренда, содержащая одно из следующих:
    * "avg": определить компонент тренда как средний (x) (по умолчанию)
    * "linefit": извлекать компонент тренда с помощью линейной регрессии.
    * "нет": нет тренда, пропустить извлечение этого компонента.    
* *Test_points*: 0 (по умолчанию) или положительный целый ряд, указывая количество очков в конце серии, чтобы исключить из процесса обучения (регрессии). Этот параметр должен быть установлен для целей прогнозирования.
* *Seasonality_threshold*: Порог для оценки сезонности, когда *сезонность* установлена для `0.6`автоматического обнаружения, порог оценки по умолчанию . Для получения дополнительной информации см [series_periods_detect.](series-periods-detectfunction.md)

**Вернуться**

 Функция возвращает следующие соответствующие серии:

* `baseline`: прогнозируемое значение серии (сумма сезонных и трендовых компонентов, см. ниже)
* `seasonal`: серия сезонных компонентов:
    * если период не обнаружен или явно установлен до 0: постоянный 0
    * при обнаружении или установке на положительную цель: медиана точек серии в той же фазе
* `trend`: серия трендовой составляющей
* `residual`: серия остаточного компонента (т.е. x - базовый)
  

**Примечания**

* Порядок выполнения компонентов:
    1. Извлеките сезонные ряды
    2. Вычтите его из x, генерируя десезонную серию
    3. Извлеките компонент тренда из серии десезонов
    4. Создайте базовый тренд и сезонный
    5. Создание остаточного q x - базовый
    
* Либо сезонность и / или тренд должен быть включен, в противном случае функция является излишним и просто возвращает базовый универсаль No 0 и остаточный х

**Подробнее о разложении серий**

Этот метод обычно применяется к временным рядам метрик, которые, как ожидается, будут проявляться периодическими и/или трендовыми поведением (например, трафиком службы и другими метриками использования), с тем чтобы либо прогнозировать будущие метрические значения и/или обнаруживать аномальные значения. Неявное предположение этого процесса регрессии состоит в том, что помимо (а-приори известных) сезонных и трендовых поведений, временные ряды стохастические и случайным образом распределены; следовательно, мы можем прогнозировать будущие метрические значения из сезонных и трендовых компонентов (игнорируя остаточную часть), в то время как мы можем обнаружить аномальные значения на основе обнаружения выбросов только на остаточной части. Более подробную информацию можно найти в [главе Временной серии Разложения](https://www.otexts.org/fpp/6) этой великой книги.

**Примеры**

**1. Еженедельная сезонность**

В следующем примере мы создаем серию с еженедельной сезонностью и без тренда, затем добавляем к ней некоторые выбросы. `series_decompose`обнаруживает авто-обнаруживает сезонность и генерирует базовый униза, который почти идентичен сезонному компоненту. Выбросы, которые мы добавили, хорошо видны в компоненте остатков.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose1.png" alt-text="Серия разлагается 1":::

**2. Еженедельная сезонность с трендом**

В этом примере мы добавляем тренд в серию из предыдущего примера. Во-первых, `series_decompose` мы запускаем с параметрами по умолчанию, в которых значение тренда `avg` по умолчанию занимает только среднее значение и не вычисляет тренд, мы видим, что генерируемый базовый уклад не содержит тренда и менее точен по сравнению с предыдущим примером, это наиболее очевидно при наблюдении тренда в остаточном варианте.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose2.png" alt-text="Разлагается серия 2":::

Далее мы запускаем тот же пример, но так как `linefit` мы ожидаем тренд в серии, мы указываем в параметр тренда. Мы видим, что положительная тенденция обнаружена и базовый гораздо ближе к входной серии. Остатки близки к нулю, и только выбросы выделяются. Мы можем видеть все компоненты серии на графике.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y, -1, 'linefit')
| render timechart  
```

:::image type="content" source="images/samples/series-decompose3.png" alt-text="Серия разлагается 3":::