---
title: Получение данных без Kusto. Принимающая библиотека — Azure обозреватель данных | Документация Майкрософт
description: В этой статье описывается способ приема данных без библиотеки Kusto. приема в Azure обозреватель данных.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 2ea7fd33a6e6ed8728fb12d53fbe76eadf8fd6b6
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862077"
---
# <a name="howto-data-ingestion-without-kustoingest-library"></a>Получение данных без Kusto. Принимающая Библиотека

## <a name="when-to-consider-not-using-kustoingest-library"></a>Когда следует расдумать, не используется ли Kusto. Принимающая Библиотека?
Как правило, использование Kusto. принимающей библиотеки должно быть предпочтительным при приеме данных в Kusto.<BR>
Если это не является параметром (обычно из-за ограничений операционной системы), то с некоторыми усилиями можно добиться почти тех же функций.<BR>
В этой статье показано, как реализовать прием в **очереди** на Kusto без зависимости от пакета Kusto. принимающий.

>**Примечание.** Приведенный ниже код написан на языке C# для упрощения примера кода с использованием пакета SDK службы хранилища Azure, библиотеки проверки подлинности ADAL и NewtonSoft. JSON.<BR>При необходимости соответствующий код можно заменить соответствующим [REST APIными](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) вызовами службы хранилища Azure, [пакетом ADAL non-.NET](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) и любым доступным пакетом обработки JSON.

## <a name="overview"></a>Обзор
В следующем примере кода демонстрируется прием данных в очереди (с помощью Kusto Управление данными Service) в Kusto без использования Kusto. принимающей библиотеки.<BR>
Это может быть полезно, если полноценная платформа .NET недоступна или недоступна из-за окружения или других ограничений.<BR>

> В этой статье рассматривается рекомендуемый режим приема для конвейеров производственного уровня, который также называется приемом в **очереди** (в терминах библиотеки Kusto. приема, соответствующая сущность — это интерфейс [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ). В этом режиме клиентский код взаимодействует со службой Kusto, публикуя сообщения уведомлений о приеме в очередь Azure, ссылку на которую можно получить из Kusto Управление данными ( Приема). Взаимодействие со службой Управление данными должно проходить проверку подлинности в **AAD**.

Структура этого потока описана в примере кода ниже и состоит из следующих элементов:
1. Создание клиента службы хранилища Azure и передача данных в большой двоичный объект
1. Получение маркера проверки подлинности для доступа к службе приема Kusto
2. Запросите службу приема Kusto, чтобы получить:
    * Ресурсы приема (очереди и контейнеры больших двоичных объектов)
    * Токен удостоверения Kusto, который будет добавлен к каждому сообщению приема
3. Отправка данных в большой двоичный объект в одном из контейнеров больших двоичных объектов, полученных из Kusto в (2)
4. Создание сообщения приема, идентифицирующего целевую базу данных и таблицу, и указание на BLOB-объект из (3)
5. Опубликовать сообщение приема, которое мы состояли в (4), в одной из очередей приема, полученных от Kusto в (2)
6. Получение всех ошибок, обнаруженных службой во время приема

В последующих разделах каждый шаг подробно описан выше.

```csharp
// A container class for ingestion resources we are going to obtain from Kusto
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Kusto ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the queues in order to prevent throttling
    PostMessageToQueue(ingestionResources.IngestionQueues.First(), ingestionMessage);

    Thread.Sleep(20000);

    // 6a. Read success notifications
    var successes = PopTopMessagesFromQueue(ingestionResources.SuccessNotificationsQueue, 32);
    foreach (var sm in successes)
    {
        Console.WriteLine($"Ingestion completed: {sm}");
    }

    // 6b. Read failure notifications
    var errors = PopTopMessagesFromQueue(ingestionResources.FailureNotificationsQueue, 32);
    foreach (var em in errors)
    {
        Console.WriteLine($"Ingestion error: {em}");
    }
}
```

## <a name="1-obtain-authentication-evidence-from-aad"></a>1. получение свидетельства проверки подлинности из AAD
Здесь мы используем ADAL для получения маркера AAD для доступа к службе Kusto Управление данными, чтобы запрашивать входные очереди.
Библиотека ADAL доступна на [платформах, отличных от Windows](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) , если это необходимо.
```csharp
// Authenticates the interactive user and retrieves AAD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT AAD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD Tenant ID or name}");

    // Acquire user token for the interactive user for Kusto:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

## <a name="2-retrieve-kusto-ingestion-resources"></a>2. получение ресурсов приема Kusto
Именно здесь вещи интереснее. В этом примере вручную создается запрос HTTP POST к Kusto Управление данными службе, запрашивающий возврат ресурсов приема.
К ним относятся очереди, прослушиваемые службой DM, а также контейнеры больших двоичных объектов для передачи данных.
Служба Управление данными будет обрабатывать любое сообщение, содержащее запросы приема, поступающие в одну из этих очередей.
```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Kusto Ingestion service using supplied Access token
internal static IngestionResourcesSnapshot RetrieveIngestionResources(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get ingestion resources\" }}";

    IngestionResourcesSnapshot ingestionResources = new IngestionResourcesSnapshot();

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        IEnumerable<JToken> tokens;

        // Input queues
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SecuredReadyForAggregationQueue')]");
        foreach (var token in tokens)
        {
            ingestionResources.IngestionQueues.Add((string) token[1]);
        }

        // Temp storage containers
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'TempStorage')]");
        foreach (var token in tokens)
        {
            ingestionResources.TempStorageContainers.Add((string)token[1]);
        }

        // Failure notifications queue
        var singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'FailedIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.FailureNotificationsQueue = (string)singleToken;

        // Success notifications queue
        singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SuccessfulIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.SuccessNotificationsQueue = (string)singleToken;
    }

    return ingestionResources;
}

// Executes a POST request on provided URI using supplied Access token and request body
internal static WebResponse SendPostRequest(string uriString, string authToken, string body)
{
    WebRequest request = WebRequest.Create(uriString);

    request.Method = "POST";
    request.ContentType = "application/json";
    request.ContentLength = body.Length;
    request.Headers.Set(HttpRequestHeader.Authorization, $"Bearer {authToken}");

    Stream bodyStream = request.GetRequestStream();
    using (StreamWriter sw = new StreamWriter(bodyStream))
    {
        sw.Write(body);
        sw.Flush();
    }

    bodyStream.Close();
    return request.GetResponse();
}
```

## <a name="obtaining-kusto-identity-token"></a>Получение маркера удостоверения Kusto
Важный шаг в авторизации приема данных — получение маркера идентификации и его присоединение к каждому принимающему сообщению. Так как прием сообщений передается в Kusto через непрямой канал (очередь Azure), нет возможности выполнять проверку подлинности в рамках внутренней системы. Механизм маркера идентификации позволяет выполнить это, выполнив Kusto свидетельство удостоверения, которое может быть проверено службой Kusto после получения сообщения приема.
```csharp
// Retrieves a Kusto identity token that will be added to every ingest message
internal static string RetrieveKustoIdentityToken(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get kusto identity token\" }}";
    string jsonPath = "Tables[0].Rows[*].[0]";

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        JToken identityToken = responseJson.SelectTokens(jsonPath).FirstOrDefault();

        return ((string)identityToken);
    }
}
```

## <a name="3-upload-data-to-azure-blob-container"></a>3. Отправка данных в контейнер больших двоичных объектов Azure
Этот шаг заключается в передаче локального файла в большой двоичный объект Azure, который позже будет передан для приема. В этом коде используется пакет SDK службы хранилища Azure, но такая зависимость невозможна, но она может быть достигнута так же, как и [Служба BLOB-объектов Azure REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api).
```csharp
// Uploads a single local file to an Azure Blob container, returns blob URI and original data size
internal static string UploadFileToBlobContainer(string filePath, string blobContainerUri, string containerName, string blobName, out long blobSize)
{
    var blobUri = new Uri(blobContainerUri);
    CloudBlobContainer blobContainer = new CloudBlobContainer(blobUri);
    CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference(blobName);

    using (Stream stream = File.OpenRead(filePath))
    {
        blockBlob.UploadFromStream(stream);
        blobSize = blockBlob.Properties.Length;
    }

    return string.Format("{0}{1}", blockBlob.Uri.AbsoluteUri, blobUri.Query);
}
```

## <a name="4-compose-kusto-ingestion-message"></a>4. составление сообщения приема Kusto
Здесь мы снова используем пакет NewtonSoft. JSON, чтобы создать допустимое сообщение запроса на прием, которое будет отправлено в очередь Azure, в которой прослушивается соответствующая служба Управление данными Kusto.
* Это минимальное значение для сообщения приема.
* Обратите внимание, что маркер удостоверения является обязательным и должен находиться `AdditionalProperties` в объекте JSON.
* При необходимости также `CsvMapping` необходимо указать `JsonMapping` свойства или.
* Дополнительные сведения см. [в статье предварительное создание сопоставлений приема](../../management/create-ingestion-mapping-command.md) .
* [Приложение а](#appendix-a-ingestion-message-internal-structure) содержит объяснение структуры сообщений приема

```csharp
internal static string PrepareIngestionMessage(string db, string table, string dataUri, long blobSizeBytes, string mappingRef, string identityToken)
{
    var message = new JObject();

    message.Add("Id", Guid.NewGuid().ToString());
    message.Add("BlobPath", dataUri);
    message.Add("RawDataSize", blobSizeBytes);
    message.Add("DatabaseName", db);
    message.Add("TableName", table);
    message.Add("RetainBlobOnSuccess", true);   // Do not delete the blob on success
    message.Add("Format", "json");              // Data is in JSON format
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef)));
    return message.ToString();
}
```

## <a name="5-post-kusto-ingestion-message-to-kusto-ingestion-queue"></a>5. Отправка сообщения приема Kusto в очередь приема Kusto
И наконец, собственно ДИД, просто Опубликуйте сообщение, созданное в выбранной очереди.<BR>
Примечание. при использовании клиента хранилища .NET оно по умолчанию кодирует сообщение в Base64. Ознакомьтесь с [документацией по хранилищу](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage).<BR>
Если вы не используете этот клиент, убедитесь, что содержимое сообщения правильно закодировано.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

## <a name="6-pop-messages-from-an-azure-queue"></a>6. сообщения POP из очереди Azure
После приема мы используем этот метод для чтения всех сообщений о сбое из соответствующей очереди, которая записывается в службу Kusto Управление данными.<BR>
В [приложении б](#appendix-b-ingestion-failure-message-structure) содержится объяснение структуры сообщений об ошибках

```csharp
internal static IEnumerable<string> PopTopMessagesFromQueue(string queueUriWithSas, int count)
{
    List<string> messages = Enumerable.Empty<string>().ToList();
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    var messagesFromQueue = queue.GetMessages(count);
    foreach (var m in messagesFromQueue)
    {
        messages.Add(m.AsString);
        queue.DeleteMessage(m);
    }

    return messages;
}
```

## <a name="appendix-a-ingestion-message-internal-structure"></a>Приложение а. Внутренняя структура сообщения приема
Сообщение, которое Kusto Служба Управление данными требуется считывать из входной очереди Azure, является документом JSON в следующем формате:

```json
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "Format" : "<csv|tsv|...>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```


|Свойство. | Описание |
|---------|-------------|
|Идентификатор |Идентификатор сообщения (GUID) |
|BlobPath |URI BLOB-объекта, включая ключ SAS, предоставляющий разрешения Kusto на чтение, запись или удаление (разрешения на запись и удаление требуются, если Kusto удаляет большой двоичный объект после завершения приема данных). |
|равдатасизе |Размер несжатых данных в байтах. Предоставление этого значения позволяет Kusto оптимизировать прием путем потенциальной статистической обработки нескольких больших двоичных объектов. Это свойство является необязательным, но если оно не указано, Kusto будет обращаться к большому двоичному объекту только для получения размера |
|имя_базы_данных |Имя целевой базы данных |
|TableName |Имя целевой таблицы |
|ретаинблобонсукцесс |Если задано `true`значение, большой двоичный объект не будет удален после успешного приема. По умолчанию — `false`. |
|Формат |Формат несжатых данных |
|флушиммедиатели |Если задано `true`значение, любые статистические вычисления будут пропущены. По умолчанию — `false`. |
|репортлевел |Уровень отчетов об успешном выполнении и ошибках: 0-сбоев, 1 — нет, 2 — все |
|репортмесод |Механизм создания отчетов: 0 — очередь, 1-Таблица |
|AdditionalProperties |Любые дополнительные свойства (Теги и т. д.); |


## <a name="appendix-b-ingestion-failure-message-structure"></a>Приложение б. Структура сообщений об ошибках приема
Следующее сообщение таблицы, которое Kusto Служба Управление данными, должно считаться из входной очереди Azure, является документом JSON в следующем формате:

|Свойство. | Описание |
|---------|-------------
|Операции |Идентификатор операции (GUID), который можно использовать для отслеживания операции на стороне службы |
|База данных |Имя целевой базы данных |
|Таблица |Имя целевой таблицы |
|фаиледон |Метка времени сбоя |
|инжестионсаурцеид |GUID, определяющий блок данных, который Kusto не смог принять |
|инжестионсаурцепас |Путь (URI) к блоку данных, который не удалось принять, Kusto |
|Подробности |Сообщение об ошибке |
|ErrorCode |Код ошибки Kusto (см. все коды ошибок [здесь](kusto-ingest-client-errors.md#ingestion-error-codes)) |
|фаилурестатус |Указывает, является ли сбой постоянным или временным |
|RootActivityId |Идентификатор корреляции Kusto (GUID), который можно использовать для отслеживания операции на стороне службы |
|оригинатесфромупдатеполици |Указывает, вызвана ли ошибка [политикой обновления транзакций](../../management/updatepolicy.md) еррорнеаус |
|шаулдретри | Указывает, может ли прием успешных попыток, если это так. |