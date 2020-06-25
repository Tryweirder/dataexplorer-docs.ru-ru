---
title: Прием из концентратора событий в Azure обозреватель данных
description: В этой статье описывается прием из концентратора событий в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dbb74d8b718b5a03fa0e9e7f4679f48c837d0842
ms.sourcegitcommit: c3bbb9a6bfd7c5506f05afb4968fdc2043a9fbbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85332542"
---
# <a name="ingest-from-event-hub"></a>Прием данных из концентратора событий

[Концентраторы событий Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) — это платформа потоковой передачи больших данных и служба приема событий. Azure обозреватель данных предлагает непрерывное получение из управляемых клиентом концентраторов событий.

## <a name="data-format"></a>Формат данных

* Данные считываются из концентратора событий в форме объектов [EVENTDATA](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* Полезные данные события могут содержать одну или несколько записей, которые будут приниматься в одном из [форматов, поддерживаемых обозреватель данных Azure](../../../ingestion-supported-formats.md).
* Данные можно сжимать с помощью `GZip` алгоритма сжатия. Должно быть указано в качестве `Compression` [Свойства приема](#ingestion-properties).

> [!Note]
> * Сжатие данных не поддерживается для сжатых форматов (Avro, Parquet, ORC).
> * Настраиваемые свойства кодирования и встроенных [систем](#event-system-properties-mapping) не поддерживаются в сжатых данных.

## <a name="ingestion-properties"></a>Свойства приема

Свойства приема указывают на процесс приема, где следует маршрутизировать данные и как обработать их. [Свойства приема](../../../ingestion-properties.md) событий можно указать с помощью свойства [EVENTDATA. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство. |Описание:|
|---|---|
| Таблица | Имя существующей целевой таблицы (с учетом регистра). Переопределяет `Table` набор в `Data Connection` колонке. |
| Формат | Формат данных. Переопределяет `Data format` набор в `Data Connection` колонке. |
| инжестионмаппингреференце | Имя существующего [сопоставления приема](../create-ingestion-mapping-command.md) , которое будет использоваться. Переопределяет `Column mapping` набор в `Data Connection` колонке.|
| сжатие; | Сжатие данных, `None` (по умолчанию) или `GZip` сжатие.|
| Кодирование | Кодировка данных, значение по умолчанию — UTF8. Может быть любой из [поддерживаемых кодировок .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |
| Теги (Предварительная версия) | Список [тегов](../extents-overview.md#extent-tagging) , связываемых с полученными данными в формате строки массива JSON. При использовании тегов возникают [проблемы с производительностью](../extents-overview.md#performance-notes-1) . |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения концентратора событий к кластеру Azure обозреватель данных необходимо указать свойства целевой таблицы (имя таблицы, формат данных, сжатие и сопоставление). Маршрутизация по умолчанию для данных также называется `static routing` .
Можно также указать свойства целевой таблицы для каждого события с помощью свойств события. Соединение будет динамически маршрутизировать данные, как указано в [свойствах EVENTDATA. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределяя статические свойства для этого события.

В следующем примере задайте сведения о концентраторе событий и отправьте данные о метриках погоды в таблицу `WeatherMetrics` .
Данные имеют `json` Формат. `mapping1`предварительно определено для таблицы `WeatherMetrics` .

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Сопоставление свойств системы событий

Свойства системы хранят свойства, заданные службой концентраторов событий в момент постановки события в очередь. При подключении к концентратору событий Azure обозреватель данных выбранные свойства будут внедрены в целевую таблицу данных в таблице.

> [!Note]
> * Системные свойства поддерживаются для событий с одной записью.
> * Системные свойства не поддерживаются в сжатых данных.
> * Для `csv` сопоставления свойства добавляются в начало записи в порядке, указанном в таблице ниже. Для `json` сопоставления свойства добавляются в соответствии с именами свойств, приведенными в следующей таблице.

### <a name="event-hub-exposes-the-following-system-properties"></a>Концентратор событий предоставляет следующие системные свойства

|Свойство. |Тип данных |Описание|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Время в очереди для события в формате UTC |
| x-opt-sequence-number |long | Логический порядковый номер события в потоке секций концентратора событий
| x-opt-offset |строка | Смещение события из потока секции концентратора событий. Идентификатор смещения уникален в пределах секции потока концентратора событий |
| Издатель x-opt- |строка | Имя издателя, если сообщение было отправлено в конечную точку издателя |
| x-opt-partition-key |строка |Ключ секции соответствующей секции, в которой хранится событие. |

Если в разделе **источника данных** таблицы были выбраны **Свойства системы событий** , необходимо включить свойства в схему таблицы и сопоставление.

**Пример схемы таблицы**

Создайте или измените схему таблицы с помощью команды схема таблицы, если данные включают:
* столбцы `Timespan` , `Metric` и`Value`  
* свойства `x-opt-enqueued-time` и`x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Пример сопоставления CSV**

Выполните следующие команды, чтобы добавить данные в начало записи.
Свойства добавляются в начало записи в порядке, указанном в таблице выше.
Порядковые значения важны для сопоставления CSV, при котором будут изменяться порядковые номера столбцов на основе сопоставленных системных свойств.

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

Добавьте данные, используя имена системных свойств, которые отображаются в списке *системных свойств событий* в колонке *подключения к данным* . Выполните команду:

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

## <a name="create-event-hub-connection"></a>Создать подключение концентратора событий

> [!Note]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="create-an-event-hub"></a>Создание концентратора событий

[Создайте концентратор событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-create), если он еще не создан. Шаблон можно найти в разделе инструкции по [созданию концентратора событий](../../../ingest-data-event-hub.md#create-an-event-hub) .

> [!Note]
> * Так как число секций неизменно, вам следует продумать масштаб заранее.
> * Группа потребителей *должна* быть уникальной для каждого потребителя. Создайте группу потребителей, выделенную для подключения к Azure обозреватель данных.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение приема данных к Azure обозреватель данных

* Через портал Azure: [подключитесь к концентратору событий](../../../ingest-data-event-hub.md#connect-to-the-event-hub).
* Использование Azure обозреватель данных Management .NET SDK: [Добавление подключения к данным концентратора событий](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)
* Использование пакета SDK Python для управления обозреватель данных Azure: [Добавление подключения к данным концентратора событий](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)
* С помощью шаблона ARM: использование [шаблона Azure Resource Manager для добавления подключения к данным концентратора событий](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Если в **данные включены сведения о маршрутизации** , *необходимо* предоставить необходимые сведения о [маршрутизации](#events-routing) как часть свойств событий.

> [!Note]
> После установки соединения он принимает данные, начиная с событий, помещенных в очередь после времени создания.

#### <a name="generating-data"></a>Создание данных

* См. [пример приложения](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , которое создает данные и отправляет их в концентратор событий.

Событие может содержать одну или несколько записей вплоть до предельного размера. В следующем примере мы отправляем два события, к каждому из которых Добавлено пять записей:

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```
