---
title: Прием из центра Интернета вещей в Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается прием из центра Интернета вещей в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: b76321fd843efe915a6fd55797bd2dc68059b004
ms.sourcegitcommit: 8ac4717dbff679991b122b09a0c1ed700562a736
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97488582"
---
# <a name="iot-hub-data-connection"></a>Подключение к данным Центра Интернета вещей

[Центр Интернета вещей Azure](/azure/iot-hub/about-iot-hub) — это управляемая служба, размещенная в облаке, которая выступает в качестве центрального центра сообщений для двунаправленного обмена данными между приложением IOT и управляемыми ими устройствами. Azure обозреватель данных обеспечивает непрерывное получение из управляемых клиентами центров Интернета вещей с помощью [встроенной конечной точки, совместимой с концентратором событий](/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

Конвейер приема Интернета вещей проходит через несколько шагов. Сначала вы создадите центр Интернета вещей и зарегистрируете в нем устройство. Затем вы создадите целевую таблицу в Azure обозреватель данных, в которой [данные в определенном формате](#data-format)будут приняты с использованием заданных [свойств приема](#ingestion-properties). Подключению центра Интернета вещей необходимо сообщить о [событиях маршрутизации](#events-routing) для подключения к таблице Azure обозреватель данных. Данные внедряются с выбранными свойствами в соответствии с [сопоставлением свойств системы событий](#event-system-properties-mapping). Этим процессом можно управлять с помощью [портал Azure](ingest-data-iot-hub.md), программно с помощью [C#](data-connection-iot-hub-csharp.md) или [Python](data-connection-iot-hub-python.md)или с помощью [шаблона Azure Resource Manager](data-connection-iot-hub-resource-manager.md).

Общие сведения о приеме данных в Azure обозреватель данных см. в статье [Обзор приема данных в azure обозреватель данных](ingest-data-overview.md).

## <a name="data-format"></a>Формат данных

* Данные считываются из конечной точки концентратора событий в форме объектов [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* См. раздел [Поддерживаемые форматы](ingestion-supported-formats.md).
    > [!NOTE]
    > Центр Интернета вещей не поддерживает формат RAW.
* См. раздел [Поддерживаемые сжатия](ingestion-supported-formats.md#supported-data-compression-formats).
  * Исходный размер несжатых данных должен быть частью метаданных большого двоичного объекта, иначе Azure обозреватель данных выполнит оценку. Ограничение размера несжатого приема на файл составляет 4 ГБ.

## <a name="ingestion-properties"></a>Свойства приема

Свойства приема указывают процессу приема, куда перенаправляются данные и как их обрабатывать. [Свойства приема](ingestion-properties.md) событий можно указать с помощью [свойства EventData. Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Задать можно следующие свойства.

|Свойство. |Описание|
|---|---|
| Таблица | Имя существующей целевой таблицы (с учетом регистра). Переопределяет `Table` набор на `Data Connection` панели. |
| Формат | Формат данных. Переопределяет `Data format` набор на `Data Connection` панели. |
| инжестионмаппингреференце | Имя существующего [сопоставления приема](kusto/management/create-ingestion-mapping-command.md) , которое будет использоваться. Переопределяет `Column mapping` набор на `Data Connection` панели.|
| Кодирование |  Кодировка данных, значение по умолчанию — UTF8. Может быть любой из [поддерживаемых кодировок .NET](/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

> [!NOTE]
> Принимаются только события, поставленные в очередь после создания подключения к данным.

## <a name="events-routing"></a>Маршрутизация событий

При настройке подключения центра Интернета вещей к кластеру Azure обозреватель данных необходимо указать свойства целевой таблицы (имя таблицы, формат данных и сопоставление). Этот параметр является маршрутизацией по умолчанию для данных, которая также называется статической маршрутизацией.
Можно также указать свойства целевой таблицы для каждого события с помощью свойств события. Соединение будет динамически маршрутизировать данные, как указано в [свойствах EVENTDATA. Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), переопределяя статические свойства для этого события.

> [!Note]
> Если в **данные включены сведения о маршрутизации** , необходимо предоставить необходимые сведения о маршрутизации как часть свойств событий.

## <a name="event-system-properties-mapping"></a>Сопоставление свойств системы событий

Системные свойства — это коллекция, используемая для хранения свойств, заданных службой центра Интернета вещей, на момент получения события. Подключение центра Интернета вещей Azure обозреватель данных внедряет выбранные свойства в целевую таблицу данных в таблице.

> [!Note]
> Для `csv` сопоставления свойства добавляются в начало записи в порядке, указанном в таблице ниже. Для `json` сопоставления свойства добавляются в соответствии с именами свойств, приведенными в следующей таблице.

### <a name="system-properties"></a>Свойства системы

Центр Интернета вещей предоставляет следующие свойства системы:

|Свойство. |Описание|
|---|---|
| message-id | Задаваемый пользователем идентификатор сообщения, используемый для шаблонов типа "запрос-ответ". |
| sequence-number | Число (уникальное для каждой очереди устройства), которое Центр Интернета вещей назначает каждому сообщению, отправленному из облака на устройство. |
| значение | Место назначения, которое указывается в сообщениях, отправляемых из облака на устройство . |
| absolute-expiry-time | Дата и время истечения срока действия сообщения. |
| iothub-enqueuedtime | Дата и время получения сообщения, отправленного с устройства в облако, Центром Интернета вещей. |
| correlation-id| Строковое свойство в сообщении ответа, которое обычно содержит идентификатор сообщения запроса в шаблонах "запрос-ответ". |
| user-id| Идентификатор, используемый для указания источника сообщений. |
| iothub-ack| Генератор отзывов на сообщения. |
| iothub-connection-device-id| Центр Интернета вещей устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Содержит идентификатор deviceId устройства, отправившего сообщение. |
| iothub-connection-auth-generation-id| Центр Интернета вещей устанавливает этот идентификатор в сообщениях, отправляемых с устройства в облако. Содержит идентификатор generationId устройства, с которого было отправлено сообщение (согласно разделу Свойства удостоверений устройств). |
| iothub-connection-auth-method| Центр Интернета вещей устанавливает этот метод проверки подлинности в сообщениях, отправляемых с устройства в облако. Это свойство содержит сведения о методе проверки подлинности, используемом для аутентификации устройства, отправляющего сообщение. |

Если в разделе **источника данных** таблицы были выбраны **Свойства системы событий** , необходимо включить свойства в схему таблицы и сопоставление.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="iot-hub-connection"></a>Подключение к Центру Интернета вещей

> [!Note]
> Для лучшей производительности создайте все ресурсы в том же регионе, что и кластер Azure обозреватель данных.

### <a name="create-an-iot-hub"></a>Создание центра Интернета вещей

[Создайте центр Интернета вещей](ingest-data-iot-hub.md#create-an-iot-hub), если у вас его еще нет. Подключение к центру Интернета вещей может осуществляться через [портал Azure](ingest-data-iot-hub.md), программно с помощью [C#](data-connection-iot-hub-csharp.md) или [Python](data-connection-iot-hub-python.md)или с помощью [шаблона Azure Resource Manager](data-connection-iot-hub-resource-manager.md).

> [!Note]
> * `device-to-cloud partitions`Число не изменяется, поэтому при задании числа секций следует учитывать долгосрочное масштабирование.
> * Группа потребителей должна быть уникальной для каждого потребителя. Создайте группу потребителей, выделенную для подключения к Azure обозреватель данных. Найдите ресурс в портал Azure и перейдите к нему, чтобы `Built-in endpoints` Добавить новую группу потребителей.

## <a name="sending-events"></a>Отправка событий

См. [Пример проекта](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/SimulatedDevice) , который имитирует устройство и создает данные.

## <a name="next-steps"></a>Дальнейшие действия

Существует несколько методов приема данных в центре Интернета вещей. Пошаговые руководства для каждого метода см. по следующим ссылкам.

* [Прием данных из центра Интернета вещей в Azure обозреватель данных](ingest-data-iot-hub.md)
* [Создание подключения к данным центра Интернета вещей для Azure обозреватель данных с помощью C# (Предварительная версия)](data-connection-iot-hub-csharp.md)
* [Создание подключения к данным центра Интернета вещей для Azure обозреватель данных с помощью Python (Предварительная версия)](data-connection-iot-hub-python.md)
* [Создание подключения к данным центра Интернета вещей для Azure обозреватель данных с помощью шаблона Azure Resource Manager](data-connection-iot-hub-resource-manager.md)
