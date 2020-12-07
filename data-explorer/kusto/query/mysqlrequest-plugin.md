---
title: подключаемый модуль mysql_request — Azure обозреватель данных
description: В этой статье описывается подключаемый модуль mysql_request в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e20e266e6fbae55c308cf13b7601277b8b0f30b2
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320746"
---
# <a name="mysql_request-plugin-preview"></a>подключаемый модуль mysql_request (Предварительная версия)

::: zone pivot="azuredataexplorer"

`mysql_request`Подключаемый модуль ОТПРАВЛЯЕТ SQL-запрос в сетевую конечную точку сервера MySQL и возвращает первый набор строк в результатах. Запрос может возвращать больше одного набора строк, но только первый набор строк становится доступным для остальной части запроса Kusto.

> [!IMPORTANT]
> `mysql_request`Подключаемый модуль находится в режиме предварительного просмотра и по умолчанию отключен.
> Чтобы включить подключаемый модуль, выполните [ `.enable plugin mysql_request` команду](../management/enable-plugin.md). Чтобы узнать, какие подключаемые модули включены, используйте [ `.show plugin` команды управления](../management/show-plugins.md).

## <a name="syntax"></a>Синтаксис

`evaluate``mysql_request` `(` *ConnectionString* `,` *SqlQuery* [ `,` *склпараметерс*]`)`

## <a name="arguments"></a>Аргументы

Имя | Тип | Описание | Обязательный/необязательный |
---|---|---|---
| *ConnectionString* | Литерал `string` | Указывает строку подключения, которая указывает на конечную точку сети сервера MySQL. См. раздел [Проверка подлинности](#username-and-password-authentication) и указание [конечной точки сети](#specify-the-network-endpoint). | Обязательно |
| *SqlQuery* | Литерал `string` | Указывает запрос, который должен быть выполнен с конечной точкой SQL. Должен возвращать один или несколько наборов строк, но только первый из них становится доступным для остальной части запроса Kusto. | Обязательно|
| *склпараметерс* | Постоянное значение типа `dynamic` | Содержит пары "ключ-значение" для передачи в качестве параметров вместе с запросом. | Необязательно |

## <a name="set-callout-policy"></a>Задать политику выноски

Подключаемый модуль делает выноски в базу данных MySql. Убедитесь, что [Политика выноски](../management/calloutpolicy.md) кластера разрешает вызов типа `mysql` в целевой *мисклдбури*.

В следующем примере показано, как определить политику выноски для базы данных MySQL. Рекомендуется ограничить политику выноски определенными конечными точками ( `my_endpoint1` , `my_endpoint2` ).

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

В следующем примере показана команда ALTER выноска для `mysql` *каллауттипе*:

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>Проверка подлинности имени пользователя и пароля

Подключаемый модуль mysql_request поддерживает только проверку имени пользователя и пароля в конечной точке сервера MySQL и не интегрируется с проверкой подлинности Azure Active Directory. 

Имя пользователя и пароль предоставляются как часть строки соединений с использованием следующих параметров:

`User ID=...; Password=...;`
    
> [!WARNING]
> Конфиденциальная или защищенная информация должна быть скрыта из строк соединения и запросов, чтобы их можно было опустить из любой трассировки Kusto. Дополнительные сведения см. в разделе [маскированные строковые литералы](scalar-data-types/string.md#obfuscated-string-literals).

## <a name="encryption-and-server-validation"></a>Шифрование и проверка сервера

В целях безопасности `SslMode` `Required` при подключении к конечной точке сети сервера MySQL безусловно устанавливается значение. В результате SQL Server должен быть настроен с действительным сертификатом сервера SSL/TLS.

## <a name="specify-the-network-endpoint"></a>Укажите конечную точку сети

Укажите конечную точку сети SQL в качестве части строки подключения.

**Синтаксис**

`Server``=` *Полное доменное имя* [ `Port` `=` *порт*]

Где:

* *FQDN* — полное доменное имя конечной точки.
* *Порт* — это TCP-порт конечной точки. По умолчанию `3306` предполагается.

## <a name="examples"></a>Примеры


### <a name="sql-query-to-azure-mysql-db"></a>SQL запрос к базе данных Azure MySQL

В следующем примере SQL-запрос отправляется в базу данных Azure MySQL. Он извлекает все записи из `[dbo].[Table]` , а затем обрабатывает результаты.

> [!NOTE]
> Этот пример не следует использовать в качестве рекомендации по отфильтру или данным проекта таким образом. Запросы SQL должны быть созданы, чтобы вернуть наименьший возможный набор данных, так как оптимизатор Kusto не пытается оптимизировать запросы между Kusto и SQL.

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>Проверка подлинности SQL с именем пользователя и паролем

Следующий пример идентичен предыдущему, но проверка подлинности SQL выполняется по имени пользователя и паролю. Для обеспечения конфиденциальности используются замаскированные строки.

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>Запрос SQL к базе данных SQL Azure с изменениями

Следующий пример отправляет SQL-запрос в базу данных SQL Azure, получая все записи из `[dbo].[Table]` , добавляя еще один `datetime` столбец, а затем обрабатывает результаты на стороне Kusto.
Он задает параметр SQL ( `@param0` ), который будет использоваться в SQL-запросе.

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

Эта возможность не поддерживается в Azure Monitor

::: zone-end
