---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: 40334f81e39317839c05ce09a2e4923be4e0747c
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502672"
---
### <a name="schema-mapping-examples"></a>Примеры сопоставления схем

**Пример сопоставления схемы таблицы**

Если данные содержат три столбца ( `Timespan` , `Metric` и `Value` ), а включаемые свойства — `x-opt-enqueued-time` и `x-opt-offset` , создайте или измените схему таблицы с помощью следующей команды:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Пример сопоставления CSV**

Выполните следующие команды, чтобы добавить данные в начало записи. Обратите внимание на порядковые значения.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Пример сопоставления JSON**

Данные добавляются с помощью сопоставления системных свойств. Выполните следующие команды:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
