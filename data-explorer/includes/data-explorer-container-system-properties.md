---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498829"
---
### <a name="event-system-properties-mapping"></a>Отображение свойств системы событий

> [!Note]
> * Свойства системы поддерживаются для событий с одной записью.
> * Для `csv` отображения свойства добавляются в начале записи. Для `json` отображения свойства добавляются в соответствии с именем, которое отображается в списке выпадающих.

При выборе **свойств системы событий** в разделе **«Источник данных»** в таблице необходимо включить следующие свойства в схему таблицы и отображение.

**Пример схемы таблицы**

Если ваши данные включают`Timespan` `Metric`три `Value`столбца (, и `x-opt-enqueued-time` `x-opt-offset`) и свойства, которые вы включаете, и, создать или изменить схему таблицы с помощью этой команды:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Пример отображения CSV**

Запустите следующие команды, чтобы добавить данные в начало записи. Обратите внимание на ординаторские значения.

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
 
**Пример отображения JSON**

Данные добавляются с помощью имен свойств системы по мере их отображаемого в списке **свойств системы событий** **событий подключения данных.** Выполните следующие команды:

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
