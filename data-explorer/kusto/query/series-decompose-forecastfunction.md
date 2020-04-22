---
title: series_decompose_forecast () - Исследователь данных Azure Документы Майкрософт
description: В этой статье описывается series_decompose_forecast () в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 67f464949bbc432e73932c4d8bff8290ef8f0f8f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663543"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

Прогноз, основанный на разложении серии.

Принимает выражение, содержащее ряд (динамический числовой массив) в качестве ввода и предсказывает значения последних точек задней линии (например, [series_decompose](series-decomposefunction.md) для получения более подробной информации о методе разложения).
 
**Синтаксис**

`series_decompose_forecast(`*Серия* `,` *Очки* `[,` *Сезонность* `,` *Тенденция* `,` *Seasonality_threshold*`])`

**Аргументы**

* *Серия*: Динамическая ячейка массива, которая представляет собой массив числовых значений. Как правило, в результате выход [серии решений](make-seriesoperator.md) или [make_list](makelist-aggfunction.md) операторов.
* *Очки*: Integer с указанием количества точек в конце серии для прогнозирования (прогноза). Эти точки исключаются из процесса обучения (регрессии).
* *Сезонность*: Несколько, контролирующий сезонный анализ, содержащий одно из следующих:
    * -1: автоматическое обнаружение сезонности с использованием [series_periods_detect](series-periods-detectfunction.md) (по умолчанию). 
    * период: положительный целый ряд, с указанием ожидаемого периода в количестве бункеров. Например, если серия находится в 1h бункеров, еженедельный период составляет 168 бункеров.
    * 0: нет сезонности (пропустить извлечение этого компонента).   
* *Тренд*: Строка, контролируя анализ тренда, содержащая одно из следующих:
    * "linefit": извлекать компонент тренда с помощью линейной регрессии (по умолчанию).    
    * "avg": определить компонент тренда как средний (x).
    * "нет": нет тренда, пропустить извлечение этого компонента.   
* *Seasonality_threshold*: Порог для оценки сезонности, когда *сезонность* установлена для `0.6`автоматического обнаружения, порог оценки по умолчанию . Для получения дополнительной информации см [series_periods_detect.](series-periods-detectfunction.md)

**Вернуться**

 Динамический массив с прогнозируемыми сериями
  

**Примечания**

* Динамический массив исходной серии ввода должен включать *слоты* для прогнозирования чирений, обычно это делается с помощью [серии решений](make-seriesoperator.md) и указания конечного времени в диапазоне, включающего временные рамки для прогнозирования.
    
* Следует либо включена сезонность и/или тренд, в противном случае функция избыточна и просто возвращает серию, наполненную нулями.

**Пример**

В следующем примере мы создаем серию из 4 недель в почасовом зерне `make-series` с еженедельной сезонностью и небольшой восходящей тенденцией, затем мы используем и добавляем еще одну пустую неделю в серию. `series_decompose_forecast`называется с неделей (24-7 баллов), он автоматически определяет сезонность и тенденцию и генерирует прогноз всего 5 недель. 

```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/samples/series-decompose-forecast.png" alt-text="Прогноз разложения серии":::
