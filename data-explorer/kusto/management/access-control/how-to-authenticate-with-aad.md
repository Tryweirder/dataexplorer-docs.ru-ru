---
title: Как выполнить аутентификацию с помощью AAD для Azure обозреватель данных Access — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается, как пройти проверку подлинности с помощью AAD для Azure обозреватель данных Access в обозреватель данных Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: b7e2120f158093e07e096b200b96ac3e265ae2e0
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82861992"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Как пройти проверку подлинности с помощью AAD для Azure обозреватель данных Access

Рекомендуемый способ доступа к Azure обозреватель данных — проверка подлинности в службе **Azure Active Directory** (иногда также НАЗЫВАЕТСЯ **Azure AD**или просто **AAD**). Это гарантирует, что Azure обозреватель данных не видит учетные данные доступа к каталогу субъекта-службы с помощью процесса с двумя этапами:

1. На первом шаге клиент взаимодействует со службой AAD, проверяет подлинность и запрашивает маркер доступа, выданный специально для конкретной конечной точки Azure обозреватель данных, к которой клиент намеревается получить доступ.
2. На втором шаге клиент выдает запросы к обозреватель данных Azure, предоставляя маркер доступа, полученный на первом шаге в качестве подтверждения удостоверения для Azure обозреватель данных.

Затем Azure обозреватель данных выполняет запрос от имени субъекта безопасности, для которого служба AAD выдала маркер доступа, и все проверки авторизации выполняются с помощью этого удостоверения.

В большинстве случаев рекомендуется использовать один из пакетов SDK Azure обозреватель данных для программного доступа к службе, так как они устраняют большую часть трудностей реализации потока выше (и многое другое). См., например, [пакет SDK для .NET](../../api/netfx/about-the-sdk.md).
Затем свойства проверки подлинности задаются [строкой подключения Kusto](../../api/connection-strings/kusto.md).
Если это невозможно, ознакомьтесь с подробными сведениями о том, как реализовать этот поток самостоятельно.

Основные сценарии проверки подлинности:

* **Клиентское приложение, проверяющее подлинность пользователя, выполнившего вход**.
  В этом сценарии интерактивное (клиент) приложение запускает запрос AAD для пользователя с учетными данными (например, имя пользователя и пароль).
  См. раздел [Проверка подлинности пользователей](#user-authentication).

* **Неавтономное приложение**.
  В этом сценарии приложение выполняется без пользователя, чтобы предоставить учетные данные, а вместо этого приложение выполняет проверку подлинности в AAD, используя некоторые учетные данные, с которыми он был настроен.
  См. раздел [Проверка подлинности приложения](#application-authentication).

* **Проверка подлинности от имени пользователя**.
  В этом сценарии, иногда называемом "веб-служба" или "веб-приложение", приложение получает маркер доступа AAD из другого приложения, а затем преобразует его в другой маркер доступа AAD, который можно использовать с обозреватель данных Azure.
  Иными словами, приложение выступает в качестве медиатораа между пользователем или приложением, которое предоставил учетные данные и службу обозреватель данных Azure.
  См. раздел [Проверка подлинности от имени пользователя](#on-behalf-of-authentication).

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Указание ресурса AAD для обозреватель данных Azure

При получении маркера доступа из AAD клиент должен сообщить AAD, к какому **ресурсу AAD** должен быть выдан маркер. Ресурс AAD конечной точки Azure обозреватель данных — это универсальный код ресурса (URI) конечной точки, который будет заносить сведения о порте и путь. Пример:

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>Указание идентификатора клиента AAD

AAD — это служба с несколькими клиентами, и каждая организация может создать объект с именем **Directory** в AAD. Объект каталога содержит объекты, связанные с безопасностью, такие как учетные записи пользователей, приложения и группы. AAD часто ссылается на каталог в качестве **клиента**. Клиенты AAD идентифицируются по идентификатору GUID (**идентификатор клиента**). Во многих случаях клиенты AAD также могут быть идентифицированы по доменному имени Организации.

Например, организация с именем Contoso может иметь идентификатор `4da81d62-e0a8-4899-adad-4349ca6bfe24` клиента и имя `contoso.com`домена.

## <a name="specifying-the-aad-authority"></a>Указание центра AAD

В AAD имеется несколько конечных точек для проверки подлинности:

* Если клиент, на котором размещается проверка подлинности, известен (иными словами, когда один из них знает, в каком каталоге AAD находится пользователь или приложение), `https://login.microsoftonline.com/{tenantId}`конечная точка AAD —.
  `{tenantId}` Здесь можно привести идентификатор клиента Организации в AAD или его доменное имя (например, `contoso.com`).

* Если клиент, на котором размещается проверка подлинности, неизвестен, можно использовать "Common" конечную точку `{tenantId}` , заменив приведенный выше на значение `common`.

> [!NOTE]
> Конечная точка AAD, используемая для проверки подлинности, также называется **URL-адресом центра AAD** или просто **центром AAD**.

## <a name="aad-token-cache"></a>Кэш маркеров AAD

При использовании Azure обозреватель данных SDK токены AAD хранятся на локальном компьютере в кэше маркеров для каждого пользователя (файл с именем **%аппдата%\кусто\токенкаче.Дата** , который может быть доступен или расшифрован только для пользователя, выполнившего вход.) Кэш проверяется на наличие маркеров перед запросом учетных данных пользователя, что значительно сокращает число попыток ввода учетных данных пользователем.

> [!NOTE]
> Кэш маркеров AAD сокращает число интерактивных запросов, которые пользователь может представить при доступе к Azure обозреватель данных, но не уменьшает их завершения. Кроме того, пользователи не могут заранее запрашивать учетные данные.
> Это означает, что один из них не должен пытаться использовать учетную запись пользователя для доступа к Azure обозреватель данных, если требуется поддержка неинтерактивных входов (например, при планировании задач), так как когда время переводится в режим запроса учетных данных пользователя, вошедшего в систему, в случае неинтерактивного входа.


## <a name="user-authentication"></a>Аутентификация пользователей

Самый простой способ доступа к Azure обозреватель данных с проверкой подлинности пользователей — использовать пакет SDK для обозреватель данных `Federated Authentication` Azure и задать для `true`свойства строки подключения Azure обозреватель данных значение. При первом использовании пакета SDK для отправки запроса службе пользователю будет предоставлена форма входа для ввода учетных данных AAD, а при успешной проверке подлинности запрос будет отправлен.

Приложения, которые не используют пакет SDK для Azure обозреватель данных, по-прежнему могут использовать клиентскую библиотеку AAD (ADAL) вместо реализации клиента протокола безопасности службы AAD. Пример из приложенияhttps://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet.NET см. в [].

Чтобы проверить подлинность пользователей Azure обозреватель данных Access, приложению сначала необходимо предоставить `Access Kusto` делегированное разрешение. Дополнительные сведения см. [в разделе Kusto Guide to подготовка приложений AAD](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application) .

В следующем кратком фрагменте кода показано использование ADAL для получения маркера пользователя AAD для доступа к обозреватель данных Azure (запуск пользовательского интерфейса входа):

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire user token for the interactive user for Kusto:
AuthenticationResult result = authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", "your client app id",
    new Uri("your client app resource id"), new PlatformParameters(PromptBehavior.Auto)).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="application-authentication"></a>Проверка подлинности приложения

В следующем кратком фрагменте кода показано, как использовать ADAL для получения маркера приложения AAD для доступа к Azure обозреватель данных. В этом потоке не отображается запрос, и приложение должно быть зарегистрировано в AAD и иметь учетные данные, необходимые для проверки подлинности приложения (например, ключ приложения, выданный AAD, или сертификат X509v2, предварительно зарегистрированный в AAD).

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire application token for Kusto:
ClientCredential applicationCredentials = new ClientCredential("your application client ID", "your application key");
AuthenticationResult result =
        authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", applicationCredentials).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="on-behalf-of-authentication"></a>Проверка подлинности от имени

В этом сценарии приложение отправило маркер доступа AAD для некоторого произвольного ресурса, управляемого приложением, и использует этот маркер для получения нового маркера доступа AAD для ресурса обозреватель данных Azure, чтобы приложение могло получить доступ к Kusto от имени участника, указанного исходным маркером доступа AAD.

Этот поток называется [потоком обмена токеном OAuth2](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04).
Как правило, для работы с AAD требуется несколько шагов настройки. в некоторых случаях (в зависимости от конфигурации клиента AAD) может потребоваться специальное согласие от администратора клиента AAD.



**Шаг 1. Установка отношений доверия между приложением и службой обозреватель данных Azure**

1. Откройте [портал Azure](https://portal.azure.com/) и убедитесь, что вы вошли в правильный клиент (см. верхний или правый угол удостоверения, используемого для входа на портал).

2. В области ресурсы щелкните **Azure Active Directory**, а затем **Регистрация приложений**.

3. Выберите приложение, использующее поток "от имени", и откройте его.

4. Щелкните **разрешения API**, а затем — **Добавить разрешение**.

5. Найдите приложение с именем **Azure обозреватель данных** и выберите его.

6. Выберите **user_impersonation или доступ к Kusto**.

7. Нажмите кнопку **Добавить разрешение**.

**Шаг 2. выполнение обмена маркерами в коде сервера**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for for Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**Шаг 3. Указание маркера для Kusto клиентской библиотеки и выполнение запросов**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}",
    clusterName,
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Проверка подлинности и авторизация веб-клиента (JavaScript)



**Конфигурация приложения AAD**

> [!NOTE]
> В дополнение к стандартным [действиям](./how-to-provision-aad-app.md) , которые необходимо выполнить для настройки приложения AAD, необходимо также включить неявный поток OAuth в приложении AAD. Это можно сделать, выбрав манифест на странице приложения на портале Azure и присвойте параметру oauth2AllowImplicitFlow значение true.

**Сведения**

Если клиент является кодом JavaScript, выполняемым в браузере пользователя, используется неявный поток предоставления. Маркер, предоставляющий клиентскому приложению доступ к службе обозреватель данных Azure, предоставляется сразу после успешной проверки подлинности как часть URI перенаправления (в фрагменте URI); в этом потоке не указан маркер обновления, поэтому клиент не может кэшировать маркер в течение длительного времени и использовать его повторно.

Как и в собственном потоке клиента, должно быть два приложения AAD (сервер и клиент) с настроенной связью между ними.

Адалжс требует получения id_token до того, как будут сделаны вызовы access_token.

Маркер доступа получается путем вызова `AuthenticationContext.login()` метода, и access_tokens получаются путем вызова. `Authenticationcontext.acquireToken()`

* Создайте AuthenticationContext с правильной конфигурацией:

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* Вызовите `authContext.login()` , прежде `acquireToken()` чем пытаться, если вы не вошли в систему. хороший способ, если вы вошли в систему или не можете вызвать метод `authContext.getCachedUser()` и увидеть, возвращается `false`ли он).
* Вызывайте `authContext.handleWindowCallback()` при каждой загрузке страницы. Это фрагмент кода, который перехватывает перенаправление обратно из AAD и извлекает маркер из URL-адреса фрагмента и кэширует его.
* Вызовите `authContext.acquireToken()` , чтобы получить фактический маркер доступа, теперь у вас есть допустимый токен идентификатора. Первым параметром для acquireToken будет URL-адрес ресурса приложения Kusto сервера AAD.

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* в Каллбакксатусессетокен можно использовать маркер в качестве токена носителя в запросе обозреватель данных Azure. Например:

```javascript
var settings = {
    url: "https://" + clusterAndRegionName + ".kusto.windows.net/v1/rest/query",
    type: "POST",
    data: JSON.stringify({
        "db": dbName,
        "csl": query,
        "properties": null
    }),
    contentType: "application/json; charset=utf-8",
    headers: { "Authorization": "Bearer " + token },
    dataType: "json",
    jsonp: false,
    success: function(data, textStatus, jqXHR) {
        if (successCallback !== undefined) {
            successCallback(data.Tables[0]);
        }

    },
    error: function(jqXHR, textStatus, errorThrown) {
        if (failureCallback !==  undefined) {
            failureCallback(textStatus, errorThrown, jqXHR.responseText);
        }

    },
};

$.ajax(settings).then(function(data) {/* do something wil the data */});
```

> Предупреждение. Если при проверке подлинности выдается следующее или аналогичное исключение:`ReferenceError: AuthenticationContext is not defined`
возможно, у вас нет AuthenticationContext в глобальном пространстве имен.
Увы, Адалжс в настоящее время имеет Недокументированное требование о том, что контекст проверки подлинности будет определен в глобальном пространстве имен.