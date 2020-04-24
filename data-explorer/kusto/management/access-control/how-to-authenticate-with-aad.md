---
title: Как аутентифицировать с помощью AAD для доступа к лазурным данным - Azure Data Explorer Документы Майкрософт
description: В этой статье описывается как-к аутентификации с AAD для Azure Data Explorer доступ в Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: dc3a87a290ac535ac475af5017170a0478cd9b54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522922"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Как аутентифицировать с Помощью AAD для доступа к исследователю данных Azure

Рекомендуемый способ доступа к Azure Data Explorer — это аутентификации службы **активного каталога Azure** (иногда также называемой **Azure AD,** или просто **AAD).** Это гарантирует, что Azure Data Explorer никогда не увидит учетные данные каталога директора, используя двухступенчатый процесс:

1. На первом этапе клиент общается с службой AAD, проверяет ее подлинность и запрашивает токен доступа, выданный специально для конкретной конечной точки Azure Data Explorer, к которому клиент намерен получить доступ.
2. На втором этапе клиент выдает запросы Исследователю данных Azure, предоставляя токен доступа, приобретенный на первом этапе, в качестве подтверждения идентификации для Azure Data Explorer.

Затем Azure Data Explorer выполняет запрос от имени директора службы безопасности, для которого AAD выдал токен доступа, и все проверки авторизации выполняются с помощью этого идентификатора.

В большинстве случаев рекомендация заключается в использовании одного из SDK Azure Data Explorer для доступа к сервису программно, так как они удаляют большую часть хлопот реализации потока выше (и многое другое). Смотрите, например, [.NET SDK](../../api/netfx/about-the-sdk.md).
Свойства аутентификации затем устанавливаются [строкой соединения Kusto.](../../api/connection-strings/kusto.md)
Если это невозможно, пожалуйста, прочитайте подробную информацию о том, как реализовать этот поток самостоятельно.

Основными сценариями проверки подлинности являются:

* **Клиентское приложение, удостоверяющее подлинность пользователя, вписавого в нее.**
  В этом случае интерактивное (клиентское) приложение запускает запрос AAD для пользователя для учетных данных (таких как имя пользователя и пароль).
  Посмотреть [проверку подлинности пользователей,](#user-authentication)

* **"Безголовый" приложение**.
  В этом случае приложение работает без присутствия пользователя для предоставления учетных данных, и вместо этого приложение проверяет подлинность как "само" к AAD, используя некоторые учетные данные, с которых оно было настроено.
  Посмотреть [проверку подлинности приложения](#application-authentication).

* **От имени аутентификации**.
  В этом сценарии, иногда называемом сценарием «веб-сервис» или «веб-приложение», приложение получает токен доступа AAD из другого приложения, а затем «преобразует» его в другой маркер доступа AAD, который можно использовать с Помощью Azure Data Explorer.
  Другими словами, приложение выступает в качестве посредника между пользователем или приложением, предоставивим учетные данные, и службой Azure Data Explorer.
  Смотрите [от имени аутентификации.](#on-behalf-of-authentication)

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Определение ресурса AAD для исследователя данных Azure

При получении токена доступа от AAD клиент должен сообщить AAD, на какой **ресурс AAD** следует выдать токен. Ресурс AAD конечной точки Azure Data Explorer является URI конечной точки, запрещающей информацию о порне и пути. Пример:

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>Указание идентификатора арендатора AAD

AAD — это мультитенантная служба, и каждая организация может создать объект под названием **каталог** в AAD. Объект каталога содержит объекты, связанные с безопасностью, такие как учетные записи пользователей, приложения и группы. AAD часто ссылается на каталог в качестве **арендатора.** Арендаторы AAD идентифицируются GUID **(идентификатор арендатора).** Во многих случаях, AAD арендаторов также могут быть определены по доменное имя организации.

Например, организация под названием "Contoso" `4da81d62-e0a8-4899-adad-4349ca6bfe24` может иметь `contoso.com`идентификатор арендатора и доменное имя.

## <a name="specifying-the-aad-authority"></a>Определение органа ААД

AAD имеет ряд конечных точек для проверки подлинности:

* Когда известно, что арендатор, размещая аутентификации основного принципала (другими словами, когда кто-то `https://login.microsoftonline.com/{tenantId}`знает, в каком каталоге AAD находится пользователь или приложение), конечная точка AAD находится в центре входной точки.
  Здесь `{tenantId}` находится либо идентификатор арендатора организации в AAD, `contoso.com`либо его доменное имя (например).

* Когда арендатор хостинг аттегеном проходит аутентификации не известно, "общая" конечная точка может быть использована путем замены `{tenantId}` выше приведенного значения. `common`

> [!NOTE]
> Конечная точка AAD, используемая для проверки подлинности, также называется **URL-адресом aAD или** просто **органом AAD.**

## <a name="aad-token-cache"></a>Кэш токенов AAD

При использовании SDK Azure Data Explorer токены AAD хранятся на локальной машине в кэше маркеров для пользователя (файл под названием **%APPDATA%-Kusto-tokenCache.data,** к которому можно получить доступ или расшифровать только в подписанном пользователе). Кэш проверяется на наличие токенов перед тем, как попросить пользователя к учетным данным, что значительно сокращает количество раз, когда пользователь должен вводить учетные данные.

> [!NOTE]
> Кэш маркеров AAD уменьшает количество интерактивных запросов, которые пользователю будет представлен с доступом к Azure Data Explorer, но не уменьшает их полное. Кроме того, пользователи не могут заранее предвидеть, когда им будет предложено получить учетные данные.
> Это означает, что не следует пытаться использовать учетную запись пользователя для доступа к Azure Data Explorer, если есть необходимость поддерживать неинтерактивные логоны (например, при планировании задач, например), потому что, когда приходит время для побуждения входа в пользователя для учетных данных, которые быстро сбой, если работает под неинтерактивным логоном.


## <a name="user-authentication"></a>Аутентификация пользователей

Самый простой способ получить доступ к Azure Data Explorer с помощью проверки подлинности пользователя — использовать SDK Azure Data Explorer и установить `Federated Authentication` свойство строки подключения Azure Data `true`Explorer. При первом использовании SDK для отправки запроса в службу пользователю будет представлена форма входа для ввода учетных данных AAD, а при успешной аутентификации запрос будет отправлен.

Приложения, не использовавающие SDK Azure Data Explorer, могут использовать клиентскую библиотеку AAD (ADAL) вместо реализации протокола протокола безопасности службы AAD. Пожалуйста,https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNetсм.

Для проверки подлинности пользователей для доступа к Azure `Access Kusto` Data Explorer сначала необходимо предоставить уполномоченное. Пожалуйста, ознакомьтесь с [руководством Kusto по приложениям AAD, которые будут предоставляться](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application) для получения подробной информации.

Следующий краткий фрагмент кода демонстрирует использование ADAL для приобретения маркера пользователя AAD для доступа к Azure Data Explorer (запуск пользовательского интерфейса журнала):

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

Следующий краткий фрагмент кода демонстрирует использование ADAL для приобретения токена приложения AAD для доступа к Azure Data Explorer. В этом потоке не представлен запрос, и приложение должно быть зарегистрировано в AAD и оснащено учетными данными, необходимыми для выполнения проверки подлинности приложения (например, ключ приложения, выданный AAD, или сертификат X509v2, который был предварительно зарегистрирован aAD).

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

В этом случае было отправлено токен доступа AAD для некоторых произвольных ресурсов, управляемых приложением, и оно использует этот маркер для приобретения нового токена доступа AAD для ресурса Azure Data Explorer, чтобы приложение могло получить доступ к Kusto от имени основного, указанного исходным токеном доступа AAD.

Этот поток называется [потоком обмена токенов OAuth2.](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04)
Как правило, для этого требуется несколько этапов конфигурации с AAD, а в некоторых случаях (в зависимости от конфигурации клиента AAD) может потребоваться специальное согласие администратора aAD-арендатора.



**Шаг 1: Установить доверительные отношения между приложением и службой Исследователя данных Azure**

1. Откройте [портал Azure](https://portal.azure.com/) и убедитесь, что вы вписались в правильный арендатор (см. верхний/правый угол для идентификации, используемой для вхром на портал).

2. На панели ресурсов щелкните **Активный каталог Azure,** а затем **регистрации приложений.**

3. Найдите приложение, используюво поток от имени, и откройте его.

4. Нажмите **разрешения API,** а затем **добавьте разрешение.**

5. Поиск приложения под названием **Azure Data Explorer** и выберите его.

6. Выберите **user_impersonation / Доступ Кусто**.

7. Нажмите **Разрешение Добавить**.

**Шаг 2: Выполните обмен токенами в вашем серверном коде**

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

**Шаг 3: Предоставьте токен в клиентскую библиотеку Kusto и выполняйте запросы**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}", 
    clusterName, 
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Проверка подлинности и авторизации веб-клиента (JavaScript)



**Конфигурация приложения AAD**

> [!NOTE]
> В дополнение к стандартным [шагам,](./how-to-provision-aad-app.md) которые необходимо выполнить для настройки приложения AAD, вы также должны включить неявный поток овта в вашем приложении AAD. Вы можете достичь этого, выбрав манифест со страницы приложения на портале azure и установить oauth2AllowImplicitFlow на истину.

**Сведения**

Когда клиент является кодом JavaScript, работающим в браузере пользователя, используется неявный поток грантов. Токен, предоставляющий клиенту доступ к службе Azure Data Explorer, предоставляется сразу же после успешной аутентификации в рамках перенаправления URI (в фрагменте URI); в этом потоке не приводится токен обновления, поэтому клиент не может кэшировать токен в течение длительных периодов времени и повторно использовать его.

Как и в потоке клиентов, должно быть два приложения AAD (Сервер и Клиент) с настроенными отношениями между ними. 

AdalJs требует получения id_token до того, как будут сделаны какие-либо access_token звонки.

Токен доступа получается по `AuthenticationContext.login()` вызову метода, а `Authenticationcontext.acquireToken()`access_tokens получены по вызову.

* Создайте authenticationContext с правильной конфигурацией:

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* Позвоните, `authContext.login()` `acquireToken()` прежде чем пытаться, если вы не вошли в систему. хороший способ OT знаю, если вы вошли или `authContext.getCachedUser()` нет, чтобы `false`позвонить и посмотреть, если он возвращается )
* Звоните `authContext.handleWindowCallback()` всякий раз, когда ваша страница загружается. Это часть кода, которая перехватывает перенаправление назад из AAD и вытаскивает маркер из URL-адреса фрагмента и кэширует его.
* Позвоните, `authContext.acquireToken()` чтобы получить фактический токен доступа, теперь, когда у вас есть действительный токен ID. Первым параметром, приобретаемымToken, будет URL-адрес ресурса приложения AAD сервера Kusto.  

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* в callbackThatUsesTheToken вы можете использовать маркер в качестве маркера носителя в запросе Azure Data Explorer. Например:

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

> Предупреждение - если вы получаете следующее или аналогичное исключение при проверке подлинности:`ReferenceError: AuthenticationContext is not defined` 
это, вероятно, потому, что у вас нет authenticationContext в глобальном пространстве имен. К сожалению, AdalJS в настоящее время имеет незадокументированное требование о том, что контекст проверки подлинности будет определен в глобальном пространстве имен.