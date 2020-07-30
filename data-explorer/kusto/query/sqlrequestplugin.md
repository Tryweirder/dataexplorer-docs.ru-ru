---
title: подключаемый модуль sql_request — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль sql_request в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e412c1ec4f08af9820018f4c8dc172bd8c748a7f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350984"
---
# <a name="sql_request-plugin"></a>Подключаемый модуль sql_request

::: zone pivot="azuredataexplorer"

  `evaluate``sql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *склпараметерс* [ `,` *Параметры*]]`)`

`sql_request`Подключаемый модуль ОТПРАВЛЯЕТ SQL-запрос в SQL Serverную конечную точку сети и возвращает первый набор строк в результатах.

## <a name="arguments"></a>Аргументы

* *ConnectionString*: `string` литерал, указывающий строку подключения, которая указывает на конечную точку сети SQL Server. См. раздел [допустимые методы проверки подлинности](#authentication) и указание [конечной точки сети](#specify-the-network-endpoint).

* *SqlQuery*: `string` литерал, указывающий запрос, который должен быть выполнен с конечной точкой SQL. Должен возвращать один или несколько наборов строк, но только первый из них становится доступным для остальной части запроса Kusto.

* *Склпараметерс*: постоянное значение типа `dynamic` , содержащее пары "ключ-значение" для передачи в качестве параметров вместе с запросом. Необязательный элемент.
  
* *Параметры*: постоянное значение типа `dynamic` , содержащее дополнительные параметры в виде пар "ключ-значение". В настоящее время `token` можно задать только, чтобы передать предоставленный вызывающим объектом маркер доступа Azure AD, который перенаправляется в конечную точку SQL для проверки подлинности. Необязательный элемент.

## <a name="examples"></a>Примеры

В следующем примере SQL-запрос отправляется в базу данных SQL Azure. Он извлекает все записи из `[dbo].[Table]` , а затем обрабатывает результаты на стороне Kusto. При проверке подлинности используется маркер Azure AD вызывающего пользователя. 

> [!NOTE]
> Этот пример не следует использовать в качестве рекомендации по отфильтру или данным проекта таким образом. Запросы SQL должны быть созданы, чтобы вернуть наименьший возможный набор данных, так как оптимизатор Kusto не пытается оптимизировать запросы между Kusto и SQL.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

Следующий пример идентичен предыдущему, за исключением того, что проверка подлинности SQL выполняется по имени пользователя и паролю. Для обеспечения конфиденциальности мы используем скрытые строки здесь.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Initial Catalog=Fabrikam;'
    h'User ID=USERNAME;'
    h'Password=PASSWORD;',
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

Следующий пример отправляет SQL-запрос в базу данных SQL Azure, получая все записи из `[dbo].[Table]` , добавляя еще один `datetime` столбец, а затем обрабатывает результаты на стороне Kusto.
Он задает параметр SQL ( `@param0` ), который будет использоваться в SQL-запросе.

```kusto
evaluate sql_request(
  'Server=tcp:contoso.database.windows.net,1433;'
    'Authentication="Active Directory Integrated";'
    'Initial Catalog=Fabrikam;',
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

## <a name="authentication"></a>Аутентификация

Подключаемый модуль sql_request поддерживает три метода проверки подлинности для конечной точки SQL Server.

### <a name="azure-ad-integrated-authentication"></a>Встроенная проверка подлинности Azure AD 

`Authentication="Active Directory Integrated"`

  Предпочтительным методом является встроенная проверка подлинности Azure AD. У этого метода есть проверка подлинности пользователя или приложения через Azure AD для Kusto. Этот же токен используется для доступа к конечной точке сети SQL Server.

### <a name="usernamepassword-authentication"></a>Проверка подлинности имени пользователя и пароля

`User ID=...; Password=...;`

  Поддержка проверки подлинности по имени пользователя и паролю предоставляется при невозможности выполнить встроенную проверку подлинности Azure AD. По возможности избегайте использования этого метода, так как секретная информация отправляется через Kusto.

### <a name="azure-ad-access-token"></a>Маркер доступа Azure AD

`dynamic({'token': h"eyJ0..."})`

   При использовании метода проверки подлинности маркера доступа Azure AD вызывающий объект создает маркер доступа, который перенаправляется Kusto в конечную точку SQL. Строка подключения не должна содержать сведения о проверке подлинности, такие как `Authentication` , `User ID` или `Password` . Вместо этого маркер доступа передается как `token` свойство в `Options` аргументе подключаемого модуля sql_request.
     
> [!WARNING]
> Строки подключения и запросы, которые включают конфиденциальную информацию или информацию, которая должна быть защищена, должны быть замаскированы для пропуска из любой трассировки Kusto.
> Дополнительные сведения см. в разделе [маскированные строковые литералы](scalar-data-types/string.md#obfuscated-string-literals).

## <a name="encryption-and-server-validation"></a>Шифрование и проверка сервера

Следующие свойства соединения принудительно применяются при подключении к сетевой конечной точке SQL Server по соображениям безопасности.

* `Encrypt`имеет значение `true` безусловно.
* `TrustServerCertificate`имеет значение `false` безусловно.

В результате SQL Server должен быть настроен с действительным сертификатом сервера SSL/TLS.

## <a name="specify-the-network-endpoint"></a>Укажите конечную точку сети

Указание конечной точки сети SQL в качестве части строки подключения является обязательным.
Правильный синтаксис:

`Server``=` `tcp:` *Полное доменное имя* [ `,` *порт*]

Где:

* *FQDN* — полное доменное имя конечной точки.
* *Порт* — это TCP-порт конечной точки. По умолчанию `1433` предполагается.

> [!NOTE]
> Другие формы указания конечной точки сети не поддерживаются.
> Нельзя опустить, например, префикс, `tcp:` даже если это возможно при использовании клиентских библиотек SQL программным способом.

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
