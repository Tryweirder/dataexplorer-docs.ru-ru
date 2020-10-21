---
title: Подключаемый модуль R (Предварительная версия) — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль R (Предварительная версия) в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ae8a82253dfd17b7d3667cd4c72648df0c42658a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242781"
---
# <a name="r-plugin-preview"></a>Подключаемый модуль R (Предварительная версия)

::: zone pivot="azuredataexplorer"

Подключаемый модуль R запускает определяемую пользователем функцию (UDF) с помощью скрипта R. Скрипт получает табличные данные в качестве входных данных и создает табличные выходные данные.
Среда выполнения подключаемого модуля размещается в [песочнице](../concepts/sandboxes.md) на узлах кластера. Песочница предоставляет изолированную и безопасную среду.

## <a name="syntax"></a>Синтаксис

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `r(` *output_schema* `,` *Скрипт* [ `,` *script_parameters*]`)`

## <a name="arguments"></a>Аргументы

* *output_schema*: `type` литерал, определяющий выходную схему табличных данных, возвращаемую кодом R.
    * Формат: `typeof(` *ColumnName* `:` *ColumnType*[,...] `)` , например: `typeof(col1:string, col2:long)` .
    * Чтобы расширить входную схему, используйте следующий синтаксис: `typeof(*, col1:string, col2:long)` .
* *Скрипт*: `string` литерал, который является допустимым скриптом R для выполнения.
* *script_parameters*: Необязательный `dynamic` литерал, который является контейнером свойств пар "имя — значение", которые передаются в скрипт R как зарезервированный `kargs` словарь. Дополнительные сведения см. в разделе [зарезервированные переменные R](#reserved-r-variables).
* *Указание. распространение*: необязательное указание на выполнение подключаемого модуля, распределенное по нескольким узлам кластера.
   Значение по умолчанию: `single`.
    * `single`: Один экземпляр скрипта будет выполняться по всем данным запроса.
    * `per_node`: Если запрос перед распределением блока R выполняется, экземпляр скрипта будет выполняться на каждом узле по содержащимся в нем данным.

## <a name="reserved-r-variables"></a>Зарезервированные переменные R

Следующие переменные зарезервированы для взаимодействия между языком запросов Kusto и кодом R:

* `df`: Входные табличные данные (значения `T` выше) в виде кадров данных R.
* `kargs`: Значение аргумента *script_parameters* в виде словаря R.
* `result`: Кадр данных R, созданный скриптом R. Значение становится табличными данными, отправляемыми любому оператору запроса Kusto, который следует за подключаемым модулем.

## <a name="enable-the-plugin"></a>Включение подключаемого модуля

* По умолчанию подключаемый модуль отключен.
* Включите или отключите подключаемый модуль в портал Azure на вкладке " **Конфигурация** " кластера. Дополнительные сведения см. [в статье Управление расширениями языка в кластере Azure обозреватель данных (Предварительная версия)](../../language-extensions.md) .

## <a name="notes-and-limitations"></a>Примечания и ограничения

* Образ "песочницы R" основан на *r 3.4.4 для Windows*и включает пакеты из [пакета r Essentials Anaconda](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* "Песочница R" ограничивает доступ к сети. Код R не может динамически устанавливать дополнительные пакеты, которые не входят в образ. Если требуются определенные пакеты, откройте **новый запрос в службу поддержки** в портал Azure.

## <a name="examples"></a>Примеры

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

## <a name="performance-tips"></a>Советы по улучшению производительности

* Сократите набор входных данных подключаемого модуля до минимально необходимого объема (столбцов или строк).
    * Используйте фильтры в исходном наборе данных с помощью языка запросов Kusto, если это возможно.
    * Чтобы выполнить вычисление для подмножества исходных столбцов, перед вызовом подключаемого модуля необходимо внести в проект только эти столбцы.
* Используется `hint.distribution = per_node` каждый раз, когда логика в скрипте является распространяемой.
    * Можно также использовать [оператор Partition](partitionoperator.md) для секционирования входного набора данных.
* При возможности используйте язык запросов Kusto для реализации логики скрипта R.

    Пример:

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

## <a name="usage-tips"></a>Советы по использованию

* Во избежание конфликтов между Kusto строками и разделителями строк R:  
    * Используйте одинарные кавычки ( `'` ) для строковых литералов Kusto в запросах Kusto.
    * Используйте двойные кавычки ( `"` ) для строковых литералов r в скриптах r.
* Используйте [оператор External Data](externaldata-operator.md) для получения содержимого скрипта, хранящегося во внешнем расположении, например в хранилище BLOB-объектов Azure или общедоступном репозитории GitHub.
  
  Пример:

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

