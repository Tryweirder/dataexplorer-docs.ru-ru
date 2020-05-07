---
title: Прием из концентратора событий в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается прием из концентратора событий в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 6b2f3b2d75bd964401ae37093405e692cfd64feb
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82861788"
---
# <a name="ingest-from-event-hub"></a>Прием данных из концентратора событий

[Концентраторы событий Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) — это платформа потоковой передачи больших данных и служба приема событий. Azure обозреватель данных предлагает непрерывный прием от управляемых клиентами концентраторов событий. 

## <a name="data-format"></a>Формат данных

* Данные считываются из концентратора событий в форме объектов [EVENTDATA](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* Полезные данные события могут содержать одну или несколько записей для приема в одном из [форматов, поддерживаемых обозреватель данных Azure](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).
* Данные можно сжимать с помощью `GZip` алгоритма сжатия. Должно быть указано в `Compression` качестве [Свойства приема](#ingestion-properties).

> [!Note]
> * Сжатие данных не поддерживается для сжатых форматов (Avro, Parquet, ORC).
> * Настраиваемые свойства кодирования и встроенных [систем](#event-system-properties-mapping) не поддерживаются в сжатых данных.

## <a name="ingestion-properties"></a>Свойства приема

Свойства приема указывают на процесс приема. Где можно направить данные и как обработать их. [Свойства приема](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) событий можно указать с помощью свойства [EVENTDATA. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство. |Описание|
|---|---|
| Таблица | Имя существующей целевой таблицы (с учетом регистра). Переопределяет `Table` набор в `Data Connection` колонке. |
| Формат | Формат данных. Переопределяет `Data format` набор в `Data Connection` колонке. |
| инжестионмаппингреференце | Имя существующего [сопоставления приема](../create-ingestion-mapping-command.md) , которое будет использоваться. Переопределяет `Column mapping` набор в `Data Connection` колонке.|
| Сжатие | Сжатие данных `None` (по умолчанию) `GZip` или сжатие.|
| Кодирование |  Кодировка данных, значение по умолчанию — UTF8. Может быть любой из [поддерживаемых кодировок .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |
| Теги (Предварительная версия) | Список [тегов](../extents-overview.md#extent-tagging) , связываемых с полученными данными в формате строки массива JSON. Обратите внимание на влияние использования тегов на [производительность](../extents-overview.md#performance-notes-1) . |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения концентратора событий к кластеру Azure обозреватель данных необходимо указать свойства целевой таблицы (имя таблицы, формат данных, сжатие и сопоставление). Это маршрутизация по умолчанию для данных, которая также называется `static routing`.
Можно также указать свойства целевой таблицы для каждого события с помощью свойств события. Соединение будет динамически маршрутизировать данные, как указано в [свойствах EVENTDATA. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределяя статические свойства для этого события.

В следующем примере задайте сведения о концентраторе событий и отправьте данные о метриках погоды `WeatherMetrics`в таблицу.
Данные имеют `json` формат. `mapping1`предварительно определено для таблицы `WeatherMetrics`:

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

Системные свойства — это коллекция, используемая для хранения свойств, заданных службой концентраторов событий, на момент постановки события в очередь. При подключении к концентратору событий Azure обозреватель данных выбранные свойства будут внедрены в целевую таблицу данных в таблице.

> [!Note]
> * Системные свойства поддерживаются для событий с одной записью.
> * Системные свойства не поддерживаются в сжатых данных.
> * Для `csv` сопоставления свойства добавляются в начало записи в порядке, указанном в таблице ниже. Для `json` сопоставления свойства добавляются в соответствии с именами свойств, приведенными в следующей таблице.

### <a name="event-hub-expose-the-following-system-properties"></a>Концентратор событий предоставляет следующие свойства системы

|Свойство. |Тип данных |Описание|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Время в очереди события в формате UTC. |
| x-opt-sequence-number |long | Логический порядковый номер события в потоке секций концентратора событий.
| x-opt-offset |строка | Смещение события относительно потока секций концентратора событий. Идентификатор смещения уникален в пределах секции потока концентратора событий. |
| Издатель x-opt- |строка | Имя издателя, если сообщение было отправлено конечной точке издателя. |
| x-opt-partition-key |строка |Ключ секции соответствующей секции, в которой хранится событие. |

Если в разделе **источника данных** таблицы были выбраны **Свойства системы событий** , необходимо включить свойства в схему таблицы и сопоставление.

**Пример схемы таблицы**

Если данные содержат три`Timespan`столбца (, `Metric`и `Value`), а включаемые свойства — `x-opt-enqueued-time` и `x-opt-offset`, создайте или измените схему таблицы с помощью следующей команды:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Пример сопоставления CSV**

Выполните следующие команды, чтобы добавить данные в начало записи. Обратите внимание на порядковые значения: свойства добавляются в начало записи в порядке, указанном в таблице выше. Это важно для сопоставления CSV, при котором порядковые номера столбцов изменятся на основе сопоставленных системных свойств.

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

Данные добавляются с помощью имен системных свойств, которые отображаются в списке **системных свойств событий** в колонке **подключения к данным** . Выполните следующие команды:

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

## <a name="create-event-hub-connection"></a>Создание подключения концентратора событий

> [!Note]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="create-an-event-hub"></a>Создание концентратора событий

[Создайте концентратор событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-create), если он еще не создан. Шаблон можно найти в разделе инструкции по [созданию концентратора событий](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub) .

> [!Note]
> * Так как число секций неизменно, вам следует продумать масштаб заранее.
> * Группа потребителей *должна* быть Унике на каждого потребителя. Создайте группу потребителей, выделенную для подключения к Azure обозреватель данных.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение приема данных к Azure обозреватель данных

* С помощью портала Azure [подключитесь к концентратору событий](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub).
* Использование Azure обозреватель данных Management .NET SDK: [Добавление подключения к данным концентратора событий](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Использование пакета SDK для управления обозреватель данных Azure для Python: [Добавление подключения к данным концентратора событий](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* Шаблон ARM: [Azure Resource Manager шаблон для добавления подключения к данным концентратора событий](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Если в **данные включены сведения о маршрутизации** , *необходимо* предоставить необходимые сведения о [маршрутизации](#events-routing) как часть свойств событий.

> [!Note]
> После установки соединения оно принимает данные, начиная с событий, помещенных в очередь после времени создания.

#### <a name="generating-data"></a>Создание данных

* См. [пример приложения](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , которое создает данные и отправляет их в концентратор событий.

Событие может содержать одну или несколько записей вплоть до предельного размера. В следующем примере мы отправляем два события, к каждому из которых Добавлено 5 записей:

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
