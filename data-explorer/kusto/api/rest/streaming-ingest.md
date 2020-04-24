---
title: Потоковая передача http запроса - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается запрос на проглатку потоковой передачи HTTP в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d14987806bbc62dbc79112700bd5b88aaa6676c3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503015"
---
# <a name="streaming-ingestion-http-request"></a>Потоковая передача запроса HTTP

## <a name="request-verb-and-resource"></a>Запрос глагола и ресурса

|Действие    |HTTP-команда|Ресурс HTTP                                               |
|----------|---------|------------------------------------------------------------|
|Прием    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Параметры запроса

| Параметр    |  Описание                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
| `{database}` | **Требуется** Название целевой базы данных для запроса на проглатку                                          |
| `{table}`    | **Требуется** Название целевой таблицы для запроса на проглатывание                                             |

## <a name="additional-parameters"></a>Дополнительные параметры
Дополнительные парамтеры отформатированы `{name}` = `{value}` как URL-запрос: пары, раздетанные & характером


| Параметр    |  Описание                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
|`streamFormat`| **Требуется** Определяет формат данных в органе запроса. Значение должно быть одним `Csv`из`Tsv``Scsv`следующих: ,`MultiJson``Avro`,`SOHsv`,`Psv``Json`,`SingleJson`, . Для получения дополнительной информации, пожалуйста, смотрите [Поддерживаемые форматы данных](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).|
|`mappingName` | **Требуется,** `streamFormat` если `Json` `SingleJson`один `MultiJson` `Avro`из , , или , **Дополнительно** в противном случае. Значение должно быть названием предварительно созданного отображения приема, определяемого на столе. Для получения дополнительной информации о картографии данных [см.](../../management/mappings.md) Способ управления заранее созданными отображами на столе описан [здесь](../../management/create-ingestion-mapping-command.md) |
              

Например, для поглования данных `Logs` csV-форматированных в таблицу в базе данных `Test` используйте следующую строку запроса:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

глотать данные jSON с предварительно созданным отображением`mylogmapping`

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```


(См. ниже для заголовков запросов и тела, чтобы включить.)

## <a name="request-headers"></a>Заголовки запросов

Следующая таблица содержит общие заголовки, используемые для выполнения операций запросов и управления.

|Стандартный заголовок  |Описание                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------------------------|
|`Accept`          |**Необязательно**. Задайте для этого параметра значение `application/json`.                                                                           |
|`Accept-Encoding` |**Необязательно**. Поддерживаемые кодировки `gzip` есть `deflate`и .                                                             |
|`Authorization`   |**Обязательно**. Посмотреть [аутентификацию](./authentication.md).                                                                |
|`Connection`      |**Необязательно**. Рекомендуется ввести `Keep-Alive` его.                                                           |
|`Content-Length`  |**Необязательно**. При известном порядке рекомендуется указать длину тела запроса.                                   |
|`Content-Encoding`|**Необязательно**. Может быть `gzip` установлен в этом случае тело должно быть gzip-сжатого                                 |
|`Expect`          |**Необязательно**. Может быть `100-Continue`установлен на .                                                                             |
|`Host`            |**Обязательно**. Установите это к полностью квалифицированного доменного имени, на `help.kusto.windows.net`которое был отправлен запрос (например, ).|

Следующая таблица содержит общие пользовательские заголовки, используемые при выполнении операций запроса и управления. Если не указано иное, эти заголовки используются только для телеметрических целей и не оказывают влияния на функциональность.

Все заголовки **не являются обязательными.** Настоятельно **рекомендуется,** однако, `x-ms-client-request-id` указать пользовательский заголовок. В некоторых сценариях (например, отмена запущенного запроса) этот заголовок является **обязательным,** поскольку он используется для идентификации запроса.


|Пользовательский заголовок           |Описание                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |(дружественное) название приложения, делающих запрос.                                                |
|`x-ms-user`             |(дружественное) имя пользователя, делавающих запрос.                                                       |
|`x-ms-user-id`          |Эквивалентно `x-ms-user`.                                                                                      |
|`x-ms-client-request-id`|Уникальный идентификатор запроса.                                                                      |
|`x-ms-client-version`   |Идентификатор (дружественной) версии для клиента, делающий запрос.                                      |

## <a name="body"></a>Текст

Тело является фактическими данными, которые должны быть проглочены. Текстовые форматы shoud используют кодирование UTF-8.

## <a name="examples"></a>Примеры

Ниже приводится запрос HTTP POST на проглование содержимого JSON:

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

Заголовки запроса:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

Тело запроса:

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

Ниже приводится запрос HTTP POST для проглатывания тех же данных, сжатых

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

Заголовки запроса:

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

Тело запроса:

```
... binary data ...
```