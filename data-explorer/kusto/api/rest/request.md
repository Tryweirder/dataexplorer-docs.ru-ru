---
title: HTTP-запрос или запрос на управление — обозреватель данных Azure
description: В этой статье описывается HTTP-запрос к запросам или управлению в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: cf9f9e5f6a9c5afca58e2637ed4e639882e3749d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337420"
---
# <a name="querymanagement-http-request"></a>HTTP-запрос для запроса или управления

## <a name="request-verb-and-resource"></a>Команда запроса и ресурс

|Действие    |HTTP-команда|Ресурс HTTP   |
|----------|---------|----------------|
|Запрос     |GET      |`/v1/rest/query`|
|Запрос     |POST     |`/v1/rest/query`|
|Запрос v2  |GET      |`/v2/rest/query`|
|Запрос v2  |POST     |`/v2/rest/query`|
|Управление|POST     |`/v1/rest/mgmt` |

Например, чтобы отправить управляющую команду ("Management") в конечную точку службы, используйте следующую строку запроса:

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

Ниже приведены заголовки и текст запроса для включения.

## <a name="request-headers"></a>Заголовки запроса

В следующей таблице содержатся общие заголовки, используемые для операций запросов и управления.

|Стандартный заголовок  |Описание                                                                                 |Обязательный/необязательный |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |Установите значение `application/json`                                                                   |Обязательно          |
|`Accept-Encoding`|Поддерживаются кодировки `gzip` и `deflate`                                                |Необязательно          |
|`Authorization`  |См. раздел [Проверка подлинности](./authentication.md)                                                   |Обязательно          |
|`Connection`     |Рекомендуется включить `Keep-Alive`                                                   |Необязательно          |
|`Content-Length` |Рекомендуется указывать длину текста запроса, если известно                            |Необязательно          |
|`Content-Type`   |Значение `application/json` с `charset=utf-8`                                              |Обязательно          |
|`Expect`         |Можно задать значение `100-Continue`                                                                |Необязательно          |
|`Host`           |Задайте в качестве полного доменного имени, на которое был отправлен запрос (например, `help.kusto.windows.net` ). |Обязательно|

В следующей таблице содержатся общие пользовательские заголовки, используемые для операций запросов и управления. Если не указано иное, эти заголовки используются только в целях телеметрии и не влияют на функциональность.

Все заголовки являются необязательными. Рекомендуется указать `x-ms-client-request-id` Пользовательский заголовок. В некоторых сценариях, таких как Отмена выполняющегося запроса, этот заголовок является обязательным, так как он используется для распознавания запроса.

|Пользовательский заголовок           |Описание                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |(Понятное) имя приложения, сделавшего запрос                                                 |
|`x-ms-user`             |(Понятное) имя пользователя, сделавшего запрос                                                        |
|`x-ms-user-id`          |То же, что `x-ms-user`                                                                                       |
|`x-ms-client-request-id`|Уникальный идентификатор запроса                                                                       |
|`x-ms-client-version`   |Идентификатор версии (понятного) для клиента, выполняющего запрос                                       |
|`x-ms-readonly`         |Если этот параметр задан, запрос запускается в режиме только для чтения, что позволяет предотвратить длительные изменения |

## <a name="request-parameters"></a>Параметры запроса

В запросе можно передать следующие параметры. Они кодируются в запросе как параметры запроса или как часть тела, в зависимости от того, используется ли параметр GET или POST.

|Параметр   |Описание                                                                                 |Обязательный/необязательный |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |Текст выполняемой команды запроса или элемента управления                                             |Обязательно          |
|`db`        |Имя базы данных в области, которая является целевым объектом команды запроса или элемента управления            |Необязательно для некоторых команд управления. <br>Требуется для других команд и всех запросов. </br>                                                                   |
|`properties`|Предоставляет свойства клиентского запроса, которые изменяют процесс обработки запроса и его результаты. Дополнительные сведения см. в разделе [Свойства клиентского запроса](../netfx/request-properties.md) .                                               | Необязательно         |

## <a name="get-query-parameters"></a>ПОЛУЧЕНИЕ параметров запроса

При использовании GET параметры запроса запроса указывают параметры запроса.

## <a name="body"></a>Текст

Если используется POST, тело запроса является отдельным документом JSON, закодированным в UTF-8, со значениями параметров запроса.

## <a name="examples"></a>Примеры

В этом примере показан запрос HTTP POST для запроса.

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

Заголовки запроса

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

Тело запроса

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

В этом примере показано, как создать запрос, который отправляет приведенный выше запрос, используя [фигурные скобки](https://curl.haxx.se/).

1. Получите маркер для проверки подлинности.

    Замените `AAD_TENANT_NAME_OR_ID` , `AAD_APPLICATION_ID` и `AAD_APPLICATION_KEY` соответствующими значениями после настройки [проверки подлинности приложения AAD](../../../provision-azure-ad-app.md) .

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    Этот фрагмент кода предоставит вам маркер носителя.

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

1. Используйте токен носителя в запросе к конечной точке запроса.

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

1. Прочтите ответ в соответствии с [этой спецификацией](response.md).