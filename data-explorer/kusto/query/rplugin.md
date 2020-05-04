---
title: Подключаемый модуль R (Предварительная версия) — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается подключаемый модуль R (Предварительная версия) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1014b9090fef60816c4bbc0a7fd9b2fdc4c22801
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737799"
---
# <a name="r-plugin-preview"></a>Подключаемый модуль R (Предварительная версия)

::: zone pivot="azuredataexplorer"

Подключаемый модуль R запускает определяемую пользователем функцию (UDF) с помощью скрипта R. R-скрипт получает табличные данные в качестве входных данных, и ожидается вывод табличного вывода.
Среда выполнения подключаемого модуля размещается в [песочнице](../concepts/sandboxes.md), изолированной и безопасной среде, работающей на узлах кластера.

### <a name="syntax"></a>Синтаксис

*T* `|` `per_node``,` *script_parameters* *output_schema* *script* [`hint.distribution` (`single`)] `r(`output_schema`,` скрипт [script_parameters] `evaluate` `=`  | `)`


### <a name="arguments"></a>Аргументы

* *output_schema*: `type` литерал, определяющий выходную схему табличных данных, возвращаемую кодом R.
    * Формат: `typeof(` *ColumnName* `:` *ColumnType* [,...] `)`, например: `typeof(col1:string, col2:long)`.
    * Чтобы расширить входную схему, используйте следующий синтаксис: `typeof(*, col1:string, col2:long)`.
* *Скрипт*: `string` литерал, который является допустимым скриптом R для выполнения.
* *script_parameters*: Необязательный `dynamic` литерал, который является контейнером свойств пар "имя-значение", которые передаются в скрипт `kargs` R как зарезервированный словарь (см. раздел [зарезервированные переменные R](#reserved-r-variables)).
* *Указание. распространение*: необязательное указание на выполнение подключаемого модуля, распределенное по нескольким узлам кластера.
   Значение по умолчанию: `single`.
    * `single`: Один экземпляр скрипта будет выполняться по всем данным запроса.
    * `per_node`: Если запрос перед распределением блока R выполняется, экземпляр скрипта будет выполняться на каждом узле по содержащимся в нем данным.


### <a name="reserved-r-variables"></a>Зарезервированные переменные R

Следующие переменные зарезервированы для взаимодействия между языком запросов Kusto и кодом R:

* `df`: Входные табличные данные (значения `T` выше) в виде кадров данных R.
* `kargs`: Значение аргумента *script_parameters* в виде словаря R.
* `result`: Кадр данных R, созданный скриптом R, значение которого преобразуется в табличные данные, отправляемые любому оператору запроса Kusto, который следует за подключаемым модулем.

### <a name="onboarding"></a>Переход


* По умолчанию подключаемый модуль отключен.
    * *Хотите включить подключаемый модуль в кластере?*
        
        * В портал Azure в кластере Azure обозреватель данных выберите **новый запрос в службу поддержки** в меню слева.
        * Для отключения подключаемого модуля требуется также открыть запрос в службу поддержки.

### <a name="notes-and-limitations"></a>Примечания и ограничения

* Образ "песочницы R" основан на *r 3.4.4 для Windows*и включает пакеты из [пакета r Essentials Anaconda](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* "Песочница R" ограничивает доступ к сети, поэтому код R не может динамически устанавливать дополнительные пакеты, не входящие в образ. Откройте **новый запрос в службу поддержки** в портал Azure, если требуются конкретные пакеты.


### <a name="examples"></a>Примеры

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="Синусная демонстрация" border="false":::

### <a name="performance-tips"></a>Советы по улучшению производительности

* Сократите набор входных данных подключаемого модуля до минимально необходимого объема (столбцов или строк).
    * Используйте фильтры в исходном наборе данных, если это возможно, используя язык запросов Kusto.
    * Чтобы выполнить вычисление для подмножества исходных столбцов, перед вызовом подключаемого модуля следует проецировать только этот столбец.
* Используется `hint.distribution = per_node` каждый раз, когда логика в скрипте является распространяемой.
    * Можно также использовать [оператор Partition](partitionoperator.md) для секционирования входного набора данных.
* При возможности используйте язык запросов Kusto для реализации логики скрипта R.

    Например:

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

### <a name="usage-tips"></a>Советы по использованию

* Чтобы избежать конфликтов между Kusto строками и символами R, мы рекомендуем использовать одинарные кавычки (`'`) для Kusto строковых литералов в запросах Kusto и двойные кавычки (`"`) для строковых литералов R в скриптах r.
* Используйте [оператор externaldata](externaldata-operator.md) для получения содержимого скрипта, хранящегося во внешнем расположении, например в хранилище BLOB-объектов Azure, общедоступном репозитории GitHub и т. д.
  
  Например:

    ```kusto    
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end

