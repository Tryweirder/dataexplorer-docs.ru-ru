---
title: узкий плагин - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается узкий плагин в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 75b211f32c15eefc60ca40b0408345be4a656652
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512246"
---
# <a name="narrow-plugin"></a>узкий плагин

```kusto
T | evaluate narrow()
```

Плагин `narrow` "не поворачивает" широкую таблицу в таблицу всего с тремя столбиками: номер строки, тип столбца и значение столбца (как). `string`

Плагин `narrow` предназначен в основном для отображения целей, так как он позволяет широкие таблицы, которые будут отображаться комфортно без необходимости горизонтальной прокрутки.

**Синтаксис**

`T | evaluate narrow()`

**Примеры**

Следующий пример показывает простой способ прочтения вывода `.show diagnostics` команды управления Kusto.

```kusto
.show diagnostics
 | evaluate narrow()
```

Результатом `.show diagnostics` является таблица с одним рядом и 33 столбцов. С помощью плагина `narrow` мы "повернуть" выход на что-то вроде этого:

Строка  | Столбец                              | Значение
-----|-------------------------------------|-----------------------------
0    | Здоровый                           | True
0    | IsRebalanceRequired                 | False
0    | IsScaleOutRequired                  | False
0    | МашиныВсего                       | 2
0    | МашиныOffline                     | 0
0    | NodeLastRestartedOn                 | 2017-03-14 10:59:18.9263023
0    | AdminLastElectedOn                  | 2017-03-14 10:58:41.6741934
0    | ClusterWarmDataЕмкостьFactorFactor       | 0.130552847673333
0    | ПровизияТотуля                        | 136
0    | DiskColdAllocationПроцент        | 5
0    | InstancesTargetНацельно-ДанныеЕмкость  | 2
0    | TotalOriginalDataSize               | 5167628070
0    | TotalExtentSize                     | 1779165230
0    | IngestionsLoadFactor                | 0
0    | IngestionsInProgress                | 0
0    | ПронизыУспех               | 100
0    | MergesInProgress                    | 0
0    | BuildVersion                        | 1.0.6281.19882
0    | Время сборки                           | 2017-03-13 11:02:44.0000000
0    | ClusterDataCapacityFactor           | 0.130552847673333
0    | IsDataWarmingRequired               | False
0    | Ребалансировка                  | 2017-03-21 09:14:53.8523455
0    | DataWarminglastrunon                | 2017-03-21 09:19:54.1438800
0    | СлиянияSuccessRate                   | 100
0    | NotHealthyReason                    | (null)
0    | IsВниманиеТребуется                 | False
0    | ВниманиеТребуетсяПричина             | (null)
0    | ProductVersion                      | KustoRelease_2017.03.13.2
0    | Неудачныеоперации              | 0
0    | Неудачныеоперации слияния               | 0
0    | MaxExtentsInSingleTable             | 64
0    | ТаблицаСМаксПробы                 | КустоМониторингУстойчивоБаза.КустоМониторингТаблиц
0    | WarmExtentSize                      | 1779165230