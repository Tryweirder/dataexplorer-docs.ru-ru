---
title: Проглокиот из концентратора событий - Azure Data Explorer (ru) Документы Майкрософт
description: В этой статье описывается ingest из концентратора событий в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ddb218e707152f529e5b547ffe06c4d3c7614811
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521511"
---
# <a name="ingest-from-event-hub"></a>Проглокизирование от концентратора событий

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) — это платформа для потоковой передачи больших данных и служба приема событий. Azure Data Explorer предлагает непрерывный проглатывание от управляемых клиентами концентраторов событий. 

## <a name="data-format"></a>Формат данных

* Данные считываемые из концентратора событий в виде объектов [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)
* Полезная нагрузка событий может содержать одну или несколько записей для попаренной в один из [форматов, поддерживаемых Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)
* Данные могут быть `GZip` сжаты с помощью алгоритма сжатия. Должно быть `Compression` указано как [свойство приема.](#ingestion-properties)

> [!Note]
> * Сжатие данных не поддерживается для сжатых форматов (Avro, Parquet, ORC).
> * Пользовательские свойства кодирования и встраивания [системы](#event-system-properties-mapping) не поддерживаются на сжатых данных.

## <a name="ingestion-properties"></a>Свойства приема

Свойства приема инструктируют процесс приема. Куда направить данные и как их обрабатывать. Вы можете указать [свойства приема](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) в течение событий с помощью [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство |Описание|
|---|---|
| Таблица | Имя (дело чувствительное) существующей целевой таблицы. Переопределяет `Table` набор на `Data Connection` лезвии. |
| Формат | Формат данных. Переопределяет `Data format` набор на `Data Connection` лезвии. |
| IngestionMappingСправка | Название существующего [отображения приема,](../create-ingestion-mapping-command.md) которое будет использоваться. Переопределяет `Column mapping` набор на `Data Connection` лезвии.|
| Сжатие | Сжатие данных(по `None` умолчанию) или `GZip` сжатие.|
| Кодирование |  Кодирование данных, по умолчанию UTF8. Может быть любой из [поддерживаемых .NET кодов.](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks) |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Разгром событий

При настройке соединения концентратора событий в кластерazных данных указывается свойства целевой таблицы (имя таблицы, формат данных, сжатие и отображение). Это по умолчанию, это для ваших данных, также именуемых `static routing`.
Вы также можете указать свойства целевой таблицы для каждого события, используя свойства событий. Соединение будет динамически маршрутизационным маршрутом данных, указанных в [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределения статических свойств для этого события.

В следующем примере установите сведения о концентраторе событий и отправьте данные метеоки погоды в таблицу. `WeatherMetrics`
Данные `json` в формате. `mapping1`предварительно определенна на `WeatherMetrics`столе:

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

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Отображение свойств системы событий

Свойства системы — это коллекция, используемая для хранения свойств, установленных службой концентраторов событий, в момент расширения события. Соединение Концентратор ажиотажа Azure Data Explorer будет встраивать выбранные свойства в посадку данных в таблице.

> [!Note]
> * Свойства системы поддерживаются для событий с одной записью.
> * Свойства системы не поддерживаются на сжатых данных.
> * Для `csv` отображения свойства добавляются в начале записи в порядке, указанном в таблице ниже. Для `json` отображения свойства добавляются в соответствии с именами свойств в следующей таблице.

### <a name="event-hub-expose-the-following-system-properties"></a>Концентратор событий предоставляет следующие свойства системы

|Свойство |Тип данных |Описание|
|---|---|---|
| x-opt-enqueued-time |DATETIME | ВРЕМЯ UTC, когда событие было закреплено. |
| x-opt-sequence-number |long | Логическое число последовательности события в потоке раздела концентратора событий.
| x-opt-offset |строка | Смещение события по отношению к потоку раздела концентратора событий. Идентификатор смещения уникален в рамках раздела потока концентратора событий. |
| x-opt-издатель |строка | Имя издателя, если сообщение было отправлено в конечную точку издателя. |
| x-opt-partition-key |строка |Ключ раздела соответствующего раздела, сохраненного событием. |

При выборе **свойств системы событий** в разделе **«Источник данных»** в таблице необходимо включить свойства в схему таблицы и отображение.

**Пример схемы таблицы**

Если ваши данные включают`Timespan` `Metric`три `Value`столбца (, и `x-opt-enqueued-time` `x-opt-offset`) и свойства, которые вы включаете, и, создать или изменить схему таблицы с помощью этой команды:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Пример отображения CSV**

Запустите следующие команды, чтобы добавить данные в начало записи. Обратите внимание на порядковые значения: свойства добавляются в начале записи в порядке, указанном в таблице выше. Это важно для отображения CSV, где ординаторы столбцов будут изменяться в зависимости от свойств системы, которые сопожаты.

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

## <a name="create-event-hub-connection"></a>Создание подключения концентратора событий

> [!Note]
> Для наилучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure Data Explorer.

### <a name="create-an-event-hub"></a>Создание концентратора событий

Если у вас его еще нет, [создайте концентратор событий.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) Шаблон можно найти в руководстве по [созданию концентратора событий.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub)

> [!Note]
> * Так как число секций неизменно, вам следует продумать масштаб заранее.
> * Потребительская группа *должна* быть uniqe на потребителя. Создайте группу потребителей, посвященную подключению Azure Data Explorer.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение к подключению к данным в Azure Data Explorer

* Через azure Портал: [Подключитесь к концентратору событий](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub).
* Использование управления Azure Data Explorer .NET SDK: [Добавить подключение к онцентру событий](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Использование управления Python SDK Internet Data Explorer: [Добавление подключения концентратора событий](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* С шаблоном ARM: [шаблон менеджера ресурсов Azure для добавления соединения данных концентратора событий](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Если **мои данные включают** выбранную информацию о маршрутах, вы *должны* предоставить необходимую информацию о [маршрутах](#events-routing) в рамках свойств событий.

> [!Note]
> После установки соединения он глотает данные, начиная с событий, окантисированных после его создания.

#### <a name="generating-data"></a>Генерация данных

* Просмотрите [пример приложения,](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) которое генерирует данные и отправляет их в концентратор событий.

Событие может содержать одну или несколько записей, вплоть до предела его размера. В следующем примере мы отправляем два события, каждый из которых имеет 5 записей, приложенных:

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