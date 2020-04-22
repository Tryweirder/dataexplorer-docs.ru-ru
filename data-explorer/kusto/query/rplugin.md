---
title: R плагин (Предварительный просмотр) - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается плагин R (Preview) в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d815a75b241f7779a5f4ee9cae626c38ed54f9f4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766007"
---
# <a name="r-plugin-preview"></a>R плагин (Предварительный просмотр)

::: zone pivot="azuredataexplorer"

Плагин R запускает пользовательскую функцию (UDF) с помощью R-скрипта. R-скрипт получает табелярные данные в качестве ввода и, как ожидается, будет производить таблетчатый выход.
Время выполнения плагина размещается в [песочнице,](../concepts/sandboxes.md)изолированной и безопасной среде, работая на узлах кластера.

### <a name="syntax"></a>Синтаксис

*Т* `|` `=` `single` `r(``,` `,` *script_parameters* *output_schema* .`hint.distribution`  | (`per_node`) output_schema *скрипт* , script_parameters `evaluate``)`


### <a name="arguments"></a>Аргументы

* *output_schema*: `type` буквальный, определяющий схему вывода таблицовых данных, возвращенных R-кодом.
    * Формат: `typeof(` *ColumnName* `:` *ColumnType* ,,... `)`, например: . `typeof(col1:string, col2:long)`
    * Чтобы расширить схему ввода, используйте следующий синтаксис: `typeof(*, col1:string, col2:long)`.
* *сценарий* `string` : буквальный, который является действительным R-скриптом, который должен быть выполнен.
* *script_parameters*: `dynamic` Дополнительный буквальный, который является собственностью мешок имя / значение `kargs` пар, которые будут переданы R сценарий в качестве зарезервированного словаря (см. [Зарезервированные R переменных](#reserved-r-variables)).
* *hint.distribution*: Дополнительный намек на выполнение плагина, который будет распределен между несколькими кластерными узлами.
   По умолчанию: `single`.
    * `single`: Один экземпляр скрипта будет работать по всем данным запроса.
    * `per_node`: Если запрос перед распределением R-блока будет запущен, экземпляр скрипта будет работать на каждом узлах над содержащимися в нем данными.


### <a name="reserved-r-variables"></a>Зарезервированные переменные R

Для взаимодействия между языком кюсто-запросов и R-кодом зарезервированы следующие переменные:

* `df`: Входные табелярные данные `T` (значения выше), как R DataFrame.
* `kargs`: Значение *аргумента script_parameters,* как словарь R.
* `result`: R DataFrame, созданный R-скриптом, значение которого становится таблетчатыми данными, которые отправляются любому оператору запроса Kusto, который следует за плагином.

### <a name="onboarding"></a>Переход


* Плагин отключен по умолчанию.
    * *Заинтересованы в включении плагина на кластере?*
        
        * На портале Azure в кластере Azure Data Explorer выберите **новый запрос поддержки** в левом меню.
        * Отключение плагина требует открытия поддержки билет, а также.

### <a name="notes-and-limitations"></a>Заметки и ограничения

* R песочнице изображение основано на *R 3.4.4 для Windows*, и включает в себя пакеты из [Anaconda R Essentials расслоение](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/).
* R песочница ограничивает доступ к сети, поэтому R-код не может динамически установить дополнительные пакеты, которые не включены в изображение. Если вам нужны определенные пакеты, откройте **новый запрос поддержки** на портале Azure.


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

:::image type="content" source="images/samples/sine-demo.png" alt-text="Синус демо":::

### <a name="performance-tips"></a>Советы по улучшению производительности

* Уменьшите набор входных данных плагина до необходимого минимума (столбцы/ряды).
    * При возможности использовать фильтры в наборе исходных данных, используя язык кюсто-запроса.
    * Чтобы выполнить расчет на подмножестве исходных столбцов, проецируйте только те столбцы, прежде чем высвунуть плагин.
* Используйте `hint.distribution = per_node` всякий раз, когда логика в скрипте утилизна.
    * Оператор [раздела](partitionoperator.md) также может использоваться для раздела набора входных данных.
* По возможности используйте язык запросов Kusto для реализации логики вашего R-скрипта.

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

### <a name="usage-tips"></a>Советы по использованию

* Чтобы избежать конфликтов между делимитаторами строки Kusto и R,`'`мы рекомендуем использовать одиночные символы цитаты ( ) для`"`буквальных букв атримов Кусто в запросах Kusto, и двойные символы цитаты ( ) для R строки буквальных в R-сценариях.
* Используйте [оператора внешних данных](externaldata-operator.md) для получения содержимого скрипта, который вы сохранили во внешнем месте, например хранилище капли Azure, общедоступное репозиторий GitHub и т.д.
  
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

Это не поддерживается в Azure Monitor

::: zone-end

