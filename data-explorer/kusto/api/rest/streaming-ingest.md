---
title: HTTP-запрос приема потоковой передачи — обозреватель данных Azure
description: В этой статье описывается HTTP-запрос приема потоковой передачи в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 672f924865cab14dff6c7d5319c3c34cca1a67ee
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862023"
---
# <a name="streaming-ingestion-http-request"></a>HTTP-запрос приема потоковой передачи

## <a name="request-verb-and-resource"></a>Команда запроса и ресурс

|Действие    |HTTP-команда|Ресурс HTTP                                               |
|----------|---------|------------------------------------------------------------|
|Прием    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Параметры запроса

| Параметр    | Описание                                                                 | Обязательный/необязательный |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   Имя целевой базы данных для запроса приема                     |  Обязательно         |
| `{table}`    |   Имя целевой таблицы для запроса приема                        |  Обязательно         |

## <a name="additional-parameters"></a>Дополнительные параметры

Дополнительные параметры форматируются как пары запросов `{name}={value}` URL-адресов, разделенные &ным символом.

| Параметр    | Описание                                                                          | Обязательный/необязательный   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| Задает формат данных в тексте запроса. Значение должно быть одним из следующих: `CSV`,`TSV`,`SCsv`,`SOHsv`,`PSV`,`JSON`,`SingleJSON`,`MultiJSON`,`Avro`. Дополнительные сведения см. в разделе [Поддерживаемые форматы данных](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).| Обязательно |
|`mappingName` | Имя предварительно созданного сопоставления приема, определенного для таблицы. Дополнительные сведения см. в разделе [сопоставления данных](../../management/mappings.md). [Здесь](../../management/create-ingestion-mapping-command.md)описывается способ управления предварительно созданными сопоставлениями в таблице.| Необязательно, но требуется `streamFormat` , если является `JSON`одним`SingleJSON`из`MultiJSON`,, или`Avro`|  |
              
Например, для приема данных в формате CSV в таблицу `Logs` в базе данных `Test`используйте:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

Для приема данных в формате JSON с предварительно созданным `mylogmapping`сопоставлением используйте:

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>Заголовки запроса

В следующей таблице содержатся общие заголовки для операций запросов и управления.

|Стандартный заголовок   | Описание                                                                               | Обязательный/необязательный | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | Задайте для `application/json`этого параметра значение.                                                     | Необязательно          |
|`Accept-Encoding` | Поддерживаемые кодировки: `gzip` и `deflate`.                                             | Необязательно          | 
|`Authorization`   | См. раздел [Проверка подлинности](./authentication.md).                                                | Обязательно          |
|`Connection`      | Включите `Keep-Alive`.                                                                      | Необязательно          |
|`Content-Length`  | Укажите длину текста запроса, если он известен.                                              | Необязательно          |
|`Content-Encoding`| Для `gzip` параметра задано значение, но текст должен быть сжат с помощью gzip                                        | Необязательно          |
|`Expect`          | Задайте значение `100-Continue`.                                                                    | Необязательно          |
|`Host`            | Задайте в качестве имени домена, которому был отправлен запрос (например, `help.kusto.windows.net`). | Обязательно          |

В следующей таблице содержатся общие пользовательские заголовки для операций запросов и управления. Если не указано иное, заголовки предназначены исключительно для телеметрии и не влияют на функциональность.

|Пользовательский заголовок           |Описание                                                                           | Обязательный/необязательный |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |(Понятное) имя приложения, сделавшего запрос.                            | Необязательно          |
|`x-ms-user`             |(Понятное) имя пользователя, сделавшего запрос.                                   | Необязательно          |
|`x-ms-user-id`          |Эквивалентно `x-ms-user`.                                                                  | Необязательно          |
|`x-ms-client-request-id`|Уникальный идентификатор запроса.                                                  | Необязательно          |
|`x-ms-client-version`   |Идентификатор версии (понятного) для клиента, выполняющего запрос. Требуется в сценариях, где он используется для распознавания запроса, например отмены выполняющегося запроса.                                                        | Необязательный/Обязательный  |

## <a name="body"></a>Текст

Текст — это фактические данные для приема. В текстовых форматах должна использоваться кодировка UTF-8.

## <a name="examples"></a>Примеры

В следующем примере показан запрос HTTP POST для приема содержимого JSON:

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

В следующем примере показан запрос HTTP POST для приема одних и тех же сжатых данных.

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
