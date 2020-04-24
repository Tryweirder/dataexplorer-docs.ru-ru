---
title: Запрос запроса/управления HTTP - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается запрос запроса запроса/управления HTTP в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 71fac7122ca51755beaa09ce3867143806e4f2b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502726"
---
# <a name="querymanagement-http-request"></a>HTTP-запрос для запроса или управления

## <a name="request-verb-and-resource"></a>Запрос глагола и ресурса

|Действие    |HTTP-команда|Ресурс HTTP   |
|----------|---------|----------------|
|Запрос     |GET      |`/v1/rest/query`|
|Запрос     |POST     |`/v1/rest/query`|
|Запрос v2  |GET      |`/v2/rest/query`|
|Запрос v2  |POST     |`/v2/rest/query`|
|Управление|POST     |`/v1/rest/mgmt` |

Например, чтобы отправить команду управления ("управление") в конечную точку службы, используйте следующую строку запроса:

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

(См. ниже для заголовков запросов и тела, чтобы включить.)

## <a name="request-headers"></a>Заголовки запросов

Следующая таблица содержит общие заголовки, используемые для выполнения операций запросов и управления.

|Стандартный заголовок  |Описание                                                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
|`Accept`         |**Обязательно**. Задайте для этого параметра значение `application/json`.                                                                                  |
|`Accept-Encoding`|**Необязательно**. Поддерживаемые кодировки `gzip` есть `deflate`и .                                                                    |
|`Authorization`  |**Обязательно**. Посмотреть [аутентификацию](./authentication.md).                                                                       |
|`Connection`     |**Необязательно**. Рекомендуется ввести `Keep-Alive` его.                                                                  |
|`Content-Length` |**Необязательно**. При известном порядке рекомендуется указать длину тела запроса.                                          |
|`Content-Type`   |**Обязательно**. Установите `application/json` это `charset=utf-8`с .                                                             |
|`Expect`         |**Необязательно**. Может быть `100-Continue`установлен на .                                                                                    |
|`Host`           |**Обязательно**. Установите это к полностью квалифицированного доменного имени, `help.kusto.windows.net`что запрос был отправлен (например, ).|

Следующая таблица содержит общие пользовательские заголовки, используемые при выполнении операций запроса и управления. Если не указано иное, эти заголовки используются только для телеметрических целей и не оказывают влияния на функциональность.

Все заголовки **не являются обязательными.** Настоятельно **рекомендуется** указать пользовательский `x-ms-client-request-id` заголовок. В некоторых сценариях (например, отмена запущенного запроса) этот заголовок является **обязательным,** поскольку он используется для идентификации запроса.


|Пользовательский заголовок           |Описание                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |(дружественное) название приложения, делающих запрос.                                                |
|`x-ms-user`             |(дружественное) имя пользователя, делавающих запрос.                                                       |
|`x-ms-user-id`          |Эквивалентно `x-ms-user`.                                                                                      |
|`x-ms-client-request-id`|Уникальный идентификатор запроса.                                                                      |
|`x-ms-client-version`   |Идентификатор (дружественной) версии для клиента, делающий запрос.                                      |
|`x-ms-readonly`         |Если указано, заставляет запрос работать в режиме только для чтения (предотвращая его от внесения длительных изменений).|

## <a name="request-parameters"></a>Параметры запроса

Следующие параметры могут быть переданы в запросе. Они закодированы в запросе в виде параметров запроса или как часть тела, в зависимости от того, используется get или POST.

* `csl`: Это **обязательный** параметр. Он содержит текст команды запроса или управления для выполнения.

* `db`: Это **дополнительный** параметр для некоторых команд управления и **обязательный** параметр для других команд управления и всех запросов. Он предоставляет название "базы данных в области" - базы данных, которая является целью команды запроса или управления.

* `properties`: Это **необязательный** параметр. Он предоставляет различные свойства запроса клиента, которые изменяют способ обработки запроса и его результаты. Для полного описания [см.](../netfx/request-properties.md)

## <a name="get-query-parameters"></a>Параметры запроса GET

При использовании GET параметры запроса определяют параметры запроса, указанные выше.

## <a name="body"></a>Текст

При использовании POST тело запроса представляет собой единый документ JSON, закодированный в UTF-8, который предоставляет значения параметров запроса, отмеченных выше.

## <a name="examples"></a>Примеры

В следующем примере показан запрос HTTP POST для запроса:

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

Заголовки запроса:

```txt
Accept: application/json
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Content-Type: application/json; charset=utf-8
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1
x-ms-user-id: EARTH\davidbg
x-ms-app: MyApp
```

Орган запроса (новые линии, введенные для ясности; они не нужны):

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

Следующий пример показывает, как создать запрос, который отправляет запрос выше с помощью [локон:](https://curl.haxx.se/)

1. Получите токен для проверки подлинности.

* `AAD_TENANT_NAME_OR_ID`заменить `AAD_APPLICATION_ID` `AAD_APPLICATION_KEY` и соответствующими значениями после настройки [аутентификации приложения AAD](../../management/access-control/how-to-provision-aad-app.md)

```
curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
  -F "grant_type=client_credentials" \
  -F "resource=https://help.kusto.windows.net" \
  -F "client_id=AAD_APPLICATION_ID" \
  -F "client_secret=AAD_APPLICATION_KEY"
```

Это предоставит вам маркер носителя:

```
{
  "token_type": "Bearer",
  "expires_in": "3599",
  "ext_expires_in":"3599", 
  "expires_on":"1578439805",
  "not_before":"1578435905",
  "resource":"https://help.kusto.windows.net",
  "access_token":"eyJ0...uXOQ"
}
```

2. Используйте маркер носителя в запросе в конечную точку запроса:

```
curl -d '{"db":"Samples","csl":"print Test=\"Hello, World!\"","properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"}}"}"' \
-H "Accept: application/json" \
-H "Authorization: Bearer eyJ0...uXOQ" \
-H "Content-Type: application/json; charset=utf-8" \
-H "Host: help.kusto.windows.net" \
-H "x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1" \
-H "x-ms-user-id: EARTH\davidbg" \
-H "x-ms-app: MyApp" \
-X POST https://help.kusto.windows.net/v2/rest/query
```

3. Читайте ответ в соответствии с [этой спецификацией](response.md).