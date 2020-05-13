---
title: Прием из центра Интернета вещей в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается прием из центра Интернета вещей в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c3ed0fa8608f2be5739c1143a648230f792e5d40
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373384"
---
# <a name="ingest-from-iot-hub"></a>Прием данных из Центра Интернета вещей

[Центр Интернета вещей Azure](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) — это управляемая служба, размещенная в облаке, которая выступает в качестве центрального центра сообщений для двунаправленного обмена данными между приложением IOT и управляемыми ими устройствами. Azure обозреватель данных обеспечивает непрерывный прием от управляемых клиентами центров Интернета вещей, используя [встроенную конечную точку, совместимую с концентратором событий](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

## <a name="data-format"></a>Формат данных

* Данные считываются из конечной точки концентратора событий в форме объектов [EVENTDATA](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* Полезные данные события могут быть в одном из [форматов, поддерживаемых обозреватель данных Azure](../../../ingestion-supported-formats.md).

## <a name="ingestion-properties"></a>Свойства приема

Свойства приема указывают на процесс приема. Где можно направить данные и как обработать их. [Свойства приема](../../../ingestion-properties.md) событий можно указать с помощью свойства [EVENTDATA. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство. |Описание|
|---|---|
| Таблица | Имя существующей целевой таблицы (с учетом регистра). Переопределяет `Table` набор в `Data Connection` колонке. |
| Формат | Формат данных. Переопределяет `Data format` набор в `Data Connection` колонке. |
| инжестионмаппингреференце | Имя существующего [сопоставления приема](../create-ingestion-mapping-command.md) , которое будет использоваться. Переопределяет `Column mapping` набор в `Data Connection` колонке.|
| Кодирование |  Кодировка данных, значение по умолчанию — UTF8. Может быть любой из [поддерживаемых кодировок .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения центра Интернета вещей к кластеру Azure обозреватель данных необходимо указать свойства целевой таблицы (имя таблицы, формат данных и сопоставление). Это маршрутизация по умолчанию для данных, которая также называется `static routing` .
Можно также указать свойства целевой таблицы для каждого события с помощью свойств события. Соединение будет динамически маршрутизировать данные, как указано в [свойствах EVENTDATA. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределяя статические свойства для этого события.

## <a name="event-system-properties-mapping"></a>Сопоставление свойств системы событий

Системные свойства — это коллекция, используемая для хранения свойств, заданных службой центров Интернета вещей, на момент получения события. Подключение центра Интернета вещей Azure обозреватель данных внедряет выбранные свойства в целевую таблицу данных в таблице.

> [!Note]
> * Для `csv` сопоставления свойства добавляются в начало записи в порядке, указанном в таблице ниже. Для `json` сопоставления свойства добавляются в соответствии с именами свойств, приведенными в следующей таблице.

### <a name="iot-hub-exposes-the-following-system-properties"></a>Центр Интернета вещей предоставляет следующие свойства системы:

|Свойство. |Описание|
|---|---|
| message-id | Задаваемый пользователем идентификатор сообщения, используемый для шаблонов типа "запрос-ответ". |
| sequence-number | Число (уникальное для каждой очереди устройства), которое Центр Интернета вещей назначает каждому сообщению, отправленному из облака на устройство. |
| значение | Место назначения, которое указывается в сообщениях, отправляемых из облака на устройство. |
| absolute-expiry-time | Дата и время истечения срока действия сообщения. |
| iothub-enqueuedtime | Дата и время получения сообщения с устройства в облако центром Интернета вещей. |
| correlation-id| Строковое свойство в сообщении ответа, которое обычно содержит идентификатор сообщения запроса в шаблонах "запрос-ответ". |
| user-id| Идентификатор, используемый для указания источника сообщений. |
| iothub-ack| Генератор отзывов на сообщения. |
| iothub-connection-device-id| Центр Интернета вещей устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Содержит идентификатор deviceId устройства, отправившего сообщение. |
| iothub-connection-auth-generation-id| Центр Интернета вещей устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Он содержит Коннектиондевицеженератионид (в зависимости от свойств идентификатора устройства) устройства, отправившего сообщение. |
| iothub-connection-auth-method| Центр Интернета вещей устанавливает этот метод проверки подлинности в сообщениях, отправляемых с устройства в облако. Это свойство содержит сведения о методе проверки подлинности, используемом для аутентификации устройства, отправляющего сообщение. |

Если в разделе **источника данных** таблицы были выбраны **Свойства системы событий** , необходимо включить свойства в схему таблицы и сопоставление.

**Пример схемы таблицы**

Если данные содержат три столбца ( `Timespan` , `Metric` и `Value` ), а включаемые свойства — `x-opt-enqueued-time` и `x-opt-offset` , создайте или измените схему таблицы с помощью следующей команды:

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

## <a name="create-iot-hub-connection"></a>Создание подключения центра Интернета вещей

> [!Note]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="create-an-iot-hub"></a>Создание центра Интернета вещей

[Создайте центр Интернета вещей](../../../ingest-data-iot-hub.md#create-an-iot-hub), если у вас его еще нет.

> [!Note]
> * `device-to-cloud partitions`Число не изменяется, поэтому при задании числа секций следует учитывать долгосрочное масштабирование.
> * Группа потребителей *должна* быть Унике на каждого потребителя. Создайте группу потребителей, выделенную для подключения Kusto. Найдите ресурс на портале Azure и перейдите к разделу `Built-in endpoints` Добавление новой группы потребителей.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение приема данных к Azure обозреватель данных

* С помощью портала Azure [Подключите таблицу обозреватель данных Azure к центру Интернета вещей](../../../ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub).
* Использование пакета SDK .NET для управления обозреватель данных Azure: [Добавление подключения к данным центра Интернета вещей](../../../data-connection-iot-hub-csharp.md#add-an-iot-hub-data-connection)
* Использование пакета SDK Python для управления обозреватель данных Azure: [Добавление подключения к данным центра Интернета вещей](../../../data-connection-iot-hub-python.md#add-an-iot-hub-data-connection)
* Шаблон ARM: [Azure Resource Manager шаблон для добавления подключения к данным центра Интернета вещей](../../../data-connection-iot-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> Если в **данные включены сведения о маршрутизации** , *необходимо* предоставить необходимые сведения о [маршрутизации](#events-routing) как часть свойств событий.

> [!Note]
> После установки соединения оно принимает данные, начиная с событий, помещенных в очередь после времени создания.

### <a name="generating-data"></a>Создание данных

* См. [Пример проекта](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) , который имитирует устройство и создает данные.