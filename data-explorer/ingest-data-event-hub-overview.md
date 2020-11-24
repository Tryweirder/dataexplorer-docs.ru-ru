---
title: Прием из концентратора событий в Azure обозреватель данных
description: В этой статье описывается прием из концентратора событий в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: b8ba6199d5353ffd34081483c2ffbbd73e88a60c
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/23/2020
ms.locfileid: "95473528"
---
# <a name="event-hub-data-connection"></a>Подключение к данным концентратора событий

[Концентраторы событий Azure](/azure/event-hubs/event-hubs-about) — это платформа потоковой передачи больших данных и служба приема событий. Azure обозреватель данных предлагает непрерывное получение из управляемых клиентом концентраторов событий.

Конвейер приема концентратора событий передает события в Azure обозреватель данных в несколько шагов. Сначала создайте концентратор событий в портал Azure. Затем вы создадите целевую таблицу в Azure обозреватель данных, в которой [данные в определенном формате](#data-format)будут приняты с использованием заданных [свойств приема](#ingestion-properties). Подключение к концентратору событий должно быть осведомлено о [маршрутизации событий](#events-routing). Данные внедряются с выбранными свойствами в соответствии с [сопоставлением свойств системы событий](#event-system-properties-mapping). [Создайте подключение](#event-hub-connection) к концентратору событий, чтобы [создать концентратор событий](#create-an-event-hub) и [Отправить события](#send-events). Этим процессом можно управлять с помощью [портал Azure](ingest-data-event-hub.md), программно с помощью [C#](data-connection-event-hub-csharp.md) или [Python](data-connection-event-hub-python.md)или с помощью [шаблона Azure Resource Manager](data-connection-event-hub-resource-manager.md).

Общие сведения о приеме данных в Azure обозреватель данных см. в статье [Обзор приема данных в azure обозреватель данных](ingest-data-overview.md).

## <a name="data-format"></a>Формат данных

* Данные считываются из концентратора событий в форме объектов [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* См. раздел [Поддерживаемые форматы](ingestion-supported-formats.md).
    > [!NOTE]
    > Концентратор событий не поддерживает формат RAW.

* Данные можно сжимать с помощью `GZip` алгоритма сжатия. Укажите `Compression` в [свойствах приема](#ingestion-properties).
   * Сжатие данных не поддерживается для сжатых форматов (Avro, Parquet, ORC).
   * Пользовательские кодировки и встроенные [Свойства системы](#event-system-properties-mapping) не поддерживаются в сжатых данных.
  
## <a name="ingestion-properties"></a>Свойства приема

Свойства приема указывают на процесс приема, где следует маршрутизировать данные и как обработать их. [Свойства приема](ingestion-properties.md) событий можно указать с помощью свойства [EVENTDATA. Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство. |Описание|
|---|---|
| Таблица | Имя существующей целевой таблицы (с учетом регистра). Переопределяет `Table` набор на `Data Connection` панели. |
| Формат | Формат данных. Переопределяет `Data format` набор на `Data Connection` панели. |
| инжестионмаппингреференце | Имя существующего [сопоставления приема](kusto/management/create-ingestion-mapping-command.md) , которое будет использоваться. Переопределяет `Column mapping` набор на `Data Connection` панели.|
| Сжатие | Сжатие данных, `None` (по умолчанию) или `GZip` сжатие.|
| Кодирование | Кодировка данных, значение по умолчанию — UTF8. Может быть любой из [поддерживаемых кодировок .NET](/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |
| Tags | Список [тегов](kusto/management/extents-overview.md#extent-tagging) , связываемых с полученными данными в формате строки массива JSON. При использовании тегов возникают [проблемы с производительностью](kusto/management/extents-overview.md#performance-notes-1) . |

> [!NOTE]
> Принимаются только события, помещенные в очередь после создания подключения к данным.

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения концентратора событий к кластеру Azure обозреватель данных необходимо указать свойства целевой таблицы (имя таблицы, формат данных, сжатие и сопоставление). Маршрутизация по умолчанию для данных также называется `static routing` .
Можно также указать свойства целевой таблицы для каждого события с помощью свойств события. Соединение будет динамически маршрутизировать данные, как указано в [свойствах EVENTDATA. Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределяя статические свойства для этого события.

В следующем примере задайте сведения о концентраторе событий и отправьте данные о метриках погоды в таблицу `WeatherMetrics` .
Данные имеют `json` Формат. `mapping1` предварительно определено для таблицы `WeatherMetrics` .

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

### <a name="system-properties"></a>Свойства системы

Концентратор событий предоставляет следующие свойства системы:

|Свойство |Тип данных |Описание|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Время в очереди для события в формате UTC |
| x-opt-sequence-number |long | Логический порядковый номер события в потоке секций концентратора событий
| x-opt-offset |строка | Смещение события из потока секции концентратора событий. Идентификатор смещения уникален в пределах секции потока концентратора событий |
| Издатель x-opt- |строка | Имя издателя, если сообщение было отправлено в конечную точку издателя |
| x-opt-partition-key |строка |Ключ секции соответствующей секции, в которой хранится событие. |

Если в разделе **источника данных** таблицы были выбраны **Свойства системы событий** , необходимо включить свойства в схему таблицы и сопоставление.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>Подключение к концентратору событий

> [!Note]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="create-an-event-hub"></a>Создание концентратора событий

[Создайте концентратор событий](/azure/event-hubs/event-hubs-create), если он еще не создан. Подключение к концентратору событий можно осуществлять с помощью [портал Azure](ingest-data-event-hub.md), программно с помощью [C#](data-connection-event-hub-csharp.md) или [Python](data-connection-event-hub-python.md)или с помощью [шаблона Azure Resource Manager](data-connection-event-hub-resource-manager.md).


> [!Note]
> * Так как число секций неизменно, вам следует продумать масштаб заранее.
> * Группа потребителей *должна* быть уникальной для каждого потребителя. Создайте группу потребителей, выделенную для подключения к Azure обозреватель данных.

## <a name="send-events"></a>Отправка событий

См. [пример приложения](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) , которое создает данные и отправляет их в концентратор событий.

Пример создания демонстрационных данных см. в статье прием [данных из концентратора событий в Azure обозреватель данных](ingest-data-event-hub.md#generate-sample-data)

## <a name="set-up-geo-disaster-recovery-solution"></a>Настройка решения географического аварийного восстановления

Концентратор событий предлагает решение [географического аварийного восстановления](/azure/event-hubs/event-hubs-geo-dr) . Обозреватель данных Azure не поддерживает `Alias` пространства имен концентратора событий. Чтобы реализовать географическое аварийное восстановление в решении, создайте два подключения к данным концентратора событий: одно для основного пространства имен и одно для дополнительного пространства имен. Обозреватель данных Azure будет прослушивать оба подключения концентратора событий.

> [!NOTE]
> Пользователь обязан реализовать отработку отказа из основного пространства имен в дополнительное пространство имен.

## <a name="next-steps"></a>Следующие шаги

* [Прием данных из концентратора событий в Azure Data Explorer](ingest-data-event-hub.md)
* [Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью C #](data-connection-event-hub-csharp.md)
* [Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью Python](data-connection-event-hub-python.md)
* [Создание подключения к данным концентратора событий для Azure обозреватель данных с помощью шаблона Azure Resource Manager](data-connection-event-hub-resource-manager.md)
