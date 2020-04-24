---
title: Глотать от концентратора IoT - Исследователь данных Azure (ru) Документы Майкрософт
description: В этой статье описывается ingest из Концентратора IoT в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 33b6f4f737657ee0a6c2712f3b7cadce0c9c0a8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521358"
---
# <a name="ingest-from-iot-hub"></a>Глоток от Концентратора IoT

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) — это управляемая служба, размещенная в облаке, которая выступает в качестве центрального концентратора сообщений для двухнаправленной связи между вашим приложением IoT и устройствами, которыми оно управляет. Azure Data Explorer предлагает непрерывный проглатывание от управляемых клиентами концентраторов IoT, используя совместимый с [событиями концентратор, встроенный в конечную точку.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)

## <a name="data-format"></a>Формат данных

* Данные считываемые из конечной точки концентратора событий в виде объектов [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)
* Полезная нагрузка событий может находиться в одном из [форматов, поддерживаемых Azure Data Explorer.](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)

## <a name="ingestion-properties"></a>Свойства приема

Свойства приема инструктируют процесс приема. Куда направить данные и как их обрабатывать. Вы можете указать [свойства приема](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) в течение событий с помощью [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство |Описание|
|---|---|
| Таблица | Имя (дело чувствительное) существующей целевой таблицы. Переопределяет `Table` набор на `Data Connection` лезвии. |
| Формат | Формат данных. Переопределяет `Data format` набор на `Data Connection` лезвии. |
| IngestionMappingСправка | Название существующего [отображения приема,](../create-ingestion-mapping-command.md) которое будет использоваться. Переопределяет `Column mapping` набор на `Data Connection` лезвии.|
| Кодирование |  Кодирование данных, по умолчанию UTF8. Может быть любой из [поддерживаемых .NET кодов.](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks) |

## <a name="events-routing"></a>Разгром событий

При настройке соединения Концентратора IoT с кластером Azure Data Explorer указывается свойства целевой таблицы (имя таблицы, формат данных и отображение). Это по умолчанию, это для ваших данных, также именуемых `static routing`.
Вы также можете указать свойства целевой таблицы для каждого события, используя свойства событий. Соединение будет динамически маршрутизационным маршрутом данных, указанных в [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределения статических свойств для этого события.

## <a name="event-system-properties-mapping"></a>Отображение свойств системы событий

Свойства системы — это коллекция, используемая для хранения свойств, установленных службой IoT Hubs, во время получения события. Соединение Концентратора IoT Azure Data Explorer встраивает выбранные свойства в посадку данных в таблице.

> [!Note]
> * Для `csv` отображения свойства добавляются в начале записи в порядке, указанном в таблице ниже. Для `json` отображения свойства добавляются в соответствии с именами свойств в следующей таблице.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Концентратор предоставляет следующие свойства системы:

|Свойство |Описание|
|---|---|
| message-id | Задаваемый пользователем идентификатор сообщения, используемый для шаблонов типа "запрос-ответ". |
| sequence-number | Число (уникальное для каждой очереди устройства), которое Центр Интернета вещей назначает каждому сообщению, отправленному из облака на устройство. |
| значение | Место назначения, которое указывается в сообщениях, отправляемых из облака на устройство. |
| absolute-expiry-time | Дата и время истечения срока действия сообщения. |
| iothub-enqueuedtime | Дата и время получения сообщения от устройства к облаку от IoT Hub. |
| correlation-id| Строковое свойство в сообщении ответа, которое обычно содержит идентификатор сообщения запроса в шаблонах "запрос-ответ". |
| user-id| Идентификатор, используемый для указания источника сообщений. |
| iothub-ack| Генератор отзывов на сообщения. |
| iothub-connection-device-id| Центр Интернета вещей устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Содержит идентификатор deviceId устройства, отправившего сообщение. |
| iothub-connection-auth-generation-id| Центр Интернета вещей устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Он содержит соединениеDeviceGenerationId (в как итог свойства устройства) устройства, которое отправило сообщение. |
| iothub-connection-auth-method| Центр Интернета вещей устанавливает этот метод проверки подлинности в сообщениях, отправляемых с устройства в облако. Это свойство содержит сведения о методе проверки подлинности, используемом для аутентификации устройства, отправляющего сообщение. |

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

## <a name="create-iot-hub-connection"></a>Создание соединения Концентратора IoT

> [!Note]
> Для наилучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure Data Explorer.

### <a name="create-an-iot-hub"></a>Создание центра Интернета вещей

Если у вас его еще нет, [создайте концентратор Iot.](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#create-an-iot-hub)

> [!Note]
> * Подсчет `device-to-cloud partitions` не изменяется, поэтому следует учитывать долгосрочную шкалу при настройке количества разделов.
> * Потребительская группа *должна* быть uniqe на потребителя. Создайте группу потребителей, посвященную соединению Kusto. Найдите свой ресурс на портале `Built-in endpoints` Azure и перейдите к добавлению новой группы потребителей.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Подключение к подключению к данным в Azure Data Explorer

* Через azure Portal: [Подключите таблицу Azure Data Explorer к концентратору IoT.](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#connect-azure-data-explorer-table-to-iot-hub)
* Использование управления Azure Data Explorer .NET SDK: [Добавить соединение данных Концентратора IoT](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-csharp#add-an-iot-hub-data-connection)
* Использование управления Python SDK Internet: [Добавление соединения концентратора IoT](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-python#add-an-iot-hub-data-connection)
* С шаблоном ARM: [шаблон управления ресурсами Azure для добавления соединения данных Iot Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-resource-manager#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> Если **мои данные включают** выбранную информацию о маршрутах, вы *должны* предоставить необходимую информацию о [маршрутах](#events-routing) в рамках свойств событий.

> [!Note]
> После установки соединения он глотает данные, начиная с событий, окантисированных после его создания.

### <a name="generating-data"></a>Генерация данных

* Просмотрите [пример проекта,](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) который имитирует устройство и генерирует данные.