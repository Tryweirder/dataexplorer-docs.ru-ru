---
title: Строки подключения Kusto — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описываются строки подключения Kusto в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 8c5ade644f383a5a0d9e846b1a3143027d1eb467
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863189"
---
# <a name="kusto-connection-strings"></a>Строки подключения Kusto

Строки подключения Kusto могут предоставить сведения, необходимые клиентскому приложению Kusto для установки подключения к конечной точке службы Kusto. Строки подключения Kusto моделируются после строк подключения ADO.NET. Это значит, что строка подключения представляет собой разделенный точками с запятой список пар "имя-значение" для параметров имени и значения, которые при необходимости начинаются с помощью одного URI.

**Пример.**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

Универсальный код ресурса (URI) предоставляет конечную точку службы для взаимодействия:

* ( `https://help.kusto.windows.net` ) — значение `Data Source` Свойства.
* `Samples`(база данных по умолчанию) — значение `Initial Catalog` Свойства.

При использовании синтаксиса имени/значения предоставляются два дополнительных свойства: 

* `Fed`свойство (также называемое `AAD Federated Security` ) имеет значение `true` .
* `Accept`для свойства задано значение `true` .

> [!NOTE]
>
> * Имена свойств не чувствительны к регистру, а пробелы между парами имен и значений игнорируются.
> * **Значения свойств чувствительны к** регистру. Значение свойства, содержащее точку с запятой ( `;` ), одинарную кавычку ( `'` ) или двойную кавычку ( `"` ), должно быть заключено в двойные кавычки.

Несколько клиентских средств Kusto поддерживают расширение по префиксу URI строки подключения, в котором они позволяют использовать сокращенный формат `@` _имя_кластера_ `/` _InitialCatalog_ .
Например, строка подключения `@help/Samples` преобразуется этими инструментами в `https://help.kusto.windows.net/Samples; Fed=true` , что указывает на три свойства ( `Data Source` , `Initial Catalog` и `AAD Federated Security` ).

В программном коде строки подключения Kusto можно анализировать и обрабатывать с помощью класса C# `Kusto.Data.KustoConnectionStringBuilder` . Этот класс проверяет все строки подключения и создает исключение во время выполнения, если проверка завершается неудачно.
Эта функция доступна во всех разновидностях пакета SDK для Kusto.

## <a name="connection-string-properties"></a>Свойства строки соединения

В следующей таблице перечислены все свойства, которые можно указать в строке подключения Kusto.
В нем перечислены программные имена (которые являются именем свойства в `Kusto.Data.KustoConnectionStringBuilder` объекте), а также дополнительные имена свойств, являющиеся псевдонимами.

### <a name="general-properties"></a>Общие свойства

| Имя свойства              | Альтернативные имена                      | Программное имя  | Описание                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| Версия клиента для трассировки |                                        | трацеклиентверсион | При трассировке версии клиента используйте это значение.   |
| источника данных                | Адрес, адрес, сетевой адрес, сервер | DataSource         | URI, указывающий конечную точку службы Kusto. Например, `https://mycluster.kusto.windows.net` или `net.tcp://localhost`.               |
| Исходный каталог            | База данных                               | InitialCatalog     | Имя базы данных, используемой по умолчанию. Например, MyDatabase|
| Согласованность запросов          | куериконсистенци                       | куериконсистенци   | Задайте значение `strongconsistency` или, `weakconsistency` чтобы определить, должен ли запрос синхронизироваться с метаданными перед выполнением |

### <a name="user-authentication-properties"></a>Свойства проверки подлинности пользователя

| Имя свойства          | Альтернативные имена                          | Программное имя | Описание                       |
|------------------------|--------------------------------------------|-------------------|-----------------------------------|
| Федеративная безопасность AAD | Федеративная безопасность, Федеративные, подача, Аадфед | федератедсекурити | Логическое значение, которое указывает клиенту выполнить Azure Active  |
| Принудительное применение MFA            | MFA, Енфорцемфа                             | енфорцемфа        | Логическое значение, которое указывает клиенту на необходимость получения маркера многофакторной проверки подлинности       |
| Идентификатор пользователя.                | UID, User                                  | UserID            | Строковое значение, которое указывает клиенту выполнить проверку подлинности пользователя с указанным именем пользователя           |
| Имя пользователя для трассировки  |                                            | трацеусернаме     | Строковое значение, которое сообщает службе, какое имя пользователя использовать при внутренней отслеживании запроса         |
| Токен пользователя             | Усртокен, UserToken                        | UserToken         | Строковое значение, которое указывает клиенту выполнить проверку подлинности пользователя с помощью указанного токена носителя.<br/>Переопределяет Аппликатионклиентид, ApplicationKey и ApplicationToken. (Если указано, пропускает фактический поток проверки подлинности клиента в пользу предоставленного маркера.)       |
| Пространство имен              | NS                                         | Пространство имен         | (Для будущего использования)  |



### <a name="application-authentication-properties"></a>Свойства проверки подлинности приложения

|Имя свойства                                     |Альтернативные имена                         |Программное имя                             |Описание      |
|--------------------------------------------------|------------------------------------------|----------------------------------------------|-----------------|
|Федеративная безопасность AAD                            |Федеративная безопасность, Федеративные, подача, Аадфед|федератедсекурити                             |Логическое значение, которое указывает клиенту выполнить федеративный метод проверки подлинности Azure Active Directory (AAD).|
|Отпечаток сертификата приложения                |аппцерт                                   |аппликатионцертификатесумбпринт              |Строковое значение, предоставляющее отпечаток сертификата клиента, используемый при использовании потока проверки подлинности сертификата клиента приложения|
|Идентификатор клиента приложения                             |AppClientId                               |аппликатионклиентид                           |Строковое значение, которое предоставляет идентификатор клиента приложения для использования при проверке подлинности|
|Ключ приложения                                   |AppKey                                    |ApplicationKey                                |Строковое значение, которое предоставляет ключ приложения для использования при проверке подлинности с помощью потока секрета приложения|
|Имя приложения для трассировки                      |трацеаппнаме                              |аппликатионнамефортраЦинг                     |Строковое значение, которое сообщает службе, какое имя приложения следует использовать при внутренней отслеживании запроса|
|Маркер приложения                                 |апптокен                                  |ApplicationToken                              |Строковое значение, которое указывает клиенту выполнить проверку подлинности приложения с помощью указанного токена носителя|
|Идентификатор центра сертификации                                      |TenantId                                  |Authority                                     |Строковое значение, представляющее имя или идентификатор клиента, в котором зарегистрировано приложение.|
|                                                  |                                          |ембеддедманажедидентити                       |Строковое значение, указывающее клиенту, какое удостоверение приложения следует использовать с проверкой подлинности с помощью управляемого удостоверения; используется `system` для указания удостоверения, назначенного системой. Это свойство нельзя задать с помощью строки подключения, только программно.|манажедсервицеидентити                        |СПИСОК ЗАДАЧ|
|Различающееся имя субъекта сертификата приложения|Субъект сертификата приложения           |аппликатионцертификатесубжектдистингуишеднаме||
|Различающееся имя издателя сертификата приложения |Издатель сертификата приложения            |аппликатионцертификатеиссуердистингуишеднаме ||
|Общедоступный сертификат для отправки сертификата приложения   |Сертификат приложения SendX5c, SendX5c  |аппликатионцертификатесендпубликцертификате   ||



### <a name="client-communication-properties"></a>Свойства связи с клиентом

|Имя свойства                      |Альтернативные имена|Программное имя  |Описание                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|Принять      ||Принять      |Логическое значение, которое запрашивает подробные объекты ошибок, возвращаемые при сбое.|
|Потоковая передача   ||Потоковая передача   |Логическое значение, которое запрашивает у клиента, не будет накапливать данные перед его предоставлением вызывающему объекту.|
|Без сжатия||Без сжатия|Логическое значение, запрашивающее клиент, не запрашивает сжатие на транспортном уровне.|

## <a name="authentication-properties-details"></a>Свойства проверки подлинности (подробности)

Одной из важных задач строки подключения является указание клиенту проверки подлинности в службе.
Следующий алгоритм обычно используется клиентами для проверки подлинности конечных точек HTTP и HTTPS:

1. Если Аадфедератедсекурити имеет значение true:
    1. Если указан параметр UserToken, используйте федеративный метод проверки подлинности AAD с указанным токеном.
    1. В противном случае, если указан параметр ApplicationToken, выполните Федеративные проверки подлинности с указанным токеном.
    1. В противном случае, если указаны Аппликатионклиентид и ApplicationKey, выполните Федеративные проверки подлинности с указанным ИДЕНТИФИКАТОРом и ключом клиента приложения.
    1. В противном случае, если указаны Аппликатионклиентид и Аппликатионцертификатесумбпринт, выполните Федеративные проверки подлинности с указанным ИДЕНТИФИКАТОРом клиента приложения и сертификатом.
    1. В противном случае выполните Федеративные проверки подлинности в удостоверении текущего пользователя, выполнившего вход (пользователь получит запрос, если это первая проверка подлинности в сеансе).

1. В противном случае проверка подлинности не выполняется.


### <a name="aad-federated-application-authentication-with-application-certificate"></a>Аутентификация федеративного приложения AAD с сертификатом приложения

1. Проверка подлинности на основе сертификата приложения поддерживается только для веб-приложений (а не для собственных клиентских приложений).
1. Веб-приложение должно быть настроено для принятия данного сертификата. [Проверка подлинности на основе сертификата приложения AAD](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
1. Веб-приложение должно быть настроено как полномочный участник в соответствующем кластере Kusto.
1. Необходимо установить сертификат с заданным отпечатком (в хранилище локального компьютера или в хранилище текущего пользователя).
1. Открытый ключ сертификата должен содержать не менее 2048 бит.

## <a name="aad-based-authentication-examples"></a>Примеры проверки подлинности на основе AAD

**Федеративная проверка подлинности AAD с использованием удостоверения пользователя, выполнившего вход в систему (при необходимости пользователю будет предложено)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;Authority Id={authority}"
```

**Федеративная проверка подлинности AAD с указанием идентификатора пользователя (при необходимости пользователю будет предложено)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var userUPN = "johndoe@contoso.com";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);
kustoConnectionStringBuilder.UserID = userUPN;

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    UserID = userUPN,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;User ID={userUPN};Authority Id={authority}"
```

**Аутентификация федеративного приложения AAD с помощью Аппликатионклиентид и ApplicationKey**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};Authority Id={authority}"
```

**Федеративная проверка подлинности AAD с использованием токена пользователя или приложения**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var access_token = "<access token obtained from AAD>"

// Recommended syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadUserTokenAuthentication(access_token, authority);

// Legacy syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    UserToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source={serviceUri};Database=NetDefaultDB;Fed=True;UserToken={access_token};Authority Id={authority}"

// Recommended syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationTokenAuthentication(access_token, authority);

// Legacy syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppToken={applicationToken};Authority Id={authority}"
```

**Использование обратного вызова поставщика маркеров (будет вызываться каждый раз, когда требуется токен)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
Func<string> tokenProviderCallback; // User-defined method to retrieve the access token

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadTokenProviderAuthentication(tokenProviderCallback);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    TokenProviderCallback = () => Task.FromResult(tokenProviderCallback()),
};
```

**Использование управляемого удостоверения**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var managedIdentity = "<managed identity>"; // For system-assigned identity use "system"

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadManagedIdentity(managedIdentity);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    EmbeddedManagedIdentity = managedIdentity,
};
```

**Использование сертификата X. 509**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
X509Certificate2 applicationCertificate = "<certificate blob>";
bool sendX5c = <desired value>; // Set too 'True' to use Trusted Issuer feature of AAD

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationCertificateAuthentication(applicationClientId, applicationCertificate, authority, sendX5c);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateBlob = applicationCertificate,
    ApplicationCertificateSendX5c = sendX5c,
    Authority = authority,
};
```

**Использование сертификата X. 509 по отпечатку (клиент пытается загрузить сертификат из локального хранилища)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};Authority Id={authority}"
```
