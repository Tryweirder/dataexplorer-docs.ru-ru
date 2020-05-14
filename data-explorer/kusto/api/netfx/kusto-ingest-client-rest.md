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
ms.openlocfilehash: 80fe504311ee847afa7244e179974d80485efe46
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373562"
---
# <a name="ingestion-without-kustoingest-library"></a>Прием без Kusto. Принимающая Библиотека

Kusto. Принимающая библиотека предпочтительна для приема данных в Azure обозреватель данных. Тем не менее можно добиться почти тех же функций, не зависят от пакета Kusto. приема.
В этой статье показано, как с помощью *приема* в службе Azure обозреватель данных для конвейеров производственного уровня.

> [!NOTE]
> Приведенный ниже код написан на языке C# и использует пакет SDK службы хранилища Azure, библиотеку проверки подлинности ADAL и NewtonSoft. JSON для упрощения примера кода. При необходимости соответствующий код можно заменить соответствующим [REST APIными](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) вызовами службы хранилища Azure, [пакетом ADAL non-.NET](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)и любым доступным пакетом обработки JSON.

В этой статье описывается рекомендуемый режим приема. Для библиотеки Kusto. приема ее соответствующая сущность является интерфейсом [икустокуеуединжестклиент](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) . В этом случае клиентский код взаимодействует со службой обозреватель данных Azure, публикуя сообщения уведомления о приеме в очередь Azure. Ссылки на сообщения берутся из службы Kusto Управление данными (также известной как прием). Взаимодействие со службой должно проходить проверку подлинности с помощью Azure Active Directory (Azure AD).

В следующем коде показано, как служба Kusto Управление данными обрабатывает прием данных в очереди без использования библиотеки Kusto. приема. Этот пример может быть полезен, если полная версия .NET недоступна или недоступна из-за среды или других ограничений.

Этот код включает шаги по созданию клиента службы хранилища Azure и передаче данных в большой двоичный объект.
Каждый шаг описывается более подробно, после примера кода.

1. [Получение маркера проверки подлинности для доступа к службе приема обозреватель данных Azure](#obtain-authentication-evidence-from-azure-ad)
1. Запросите службу приема обозреватель данных Azure, чтобы получить:
    * [Ресурсы приема (очереди и контейнеры больших двоичных объектов)](#retrieve-azure-data-explorer-ingestion-resources)
    * [Токен удостоверения Kusto, который будет добавлен к каждому сообщению приема](#obtain-a-kusto-identity-token)
1. [Отправка данных в большой двоичный объект в одном из контейнеров больших двоичных объектов, полученных из Kusto в (2)](#upload-data-to-the-azure-blob-container)
1. [Составление сообщения приема, идентифицирующего целевую базу данных и таблицу и указывающая на большой двоичный объект из (3)](#compose-the-azure-data-explorer-ingestion-message)
1. [Публикация сообщения приема, которое мы состояли в (4), в очередь приема, полученную из обозреватель данных Azure в (2)](#post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue)**
1. [Получение всех ошибок, обнаруженных службой во время приема](#check-for-error-messages-from-the-azure-queue)

```csharp
// A container class for ingestion resources we are going to obtain from Azure Data Explorer
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Azure Data Explorer ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Azure Data Explorer.
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

## <a name="using-queued-ingestion-to-azure-data-explorer-for-production-grade-pipelines"></a>Использование приема в очереди в Azure обозреватель данных для конвейеров производственного уровня

### <a name="obtain-authentication-evidence-from-azure-ad"></a>Получение свидетельства проверки подлинности из Azure AD

Здесь мы используем ADAL, чтобы получить маркер Azure AD для доступа к службе Управление данными Kusto и запросить входные очереди.
Библиотека ADAL доступна на [платформах, отличных от Windows](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) , если это необходимо.

```csharp
// Authenticates the interactive user and retrieves Azure AD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT Azure AD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD Tenant ID or name}");

    // Acquire user token for the interactive user for Azure Data Explorer:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

### <a name="retrieve-azure-data-explorer-ingestion-resources"></a>Получение ресурсов приема обозреватель данных Azure

Вручную создайте запрос HTTP POST к службе Управление данными, запросив возврат ресурсов приема. Эти ресурсы включают очереди, прослушиваемые службой DM, и контейнеры больших двоичных объектов для передачи данных.
Служба Управление данными будет обрабатывать все сообщения, содержащие запросы приема, поступающие в одну из этих очередей.

```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Azure Data Explorer Ingestion service using supplied Access token
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

### <a name="obtain-a-kusto-identity-token"></a>Получение маркера удостоверения Kusto

Прием сообщений передаются в Azure обозреватель данных через непосредственный канал (очередь Azure), что делает невозможным проверку подлинности в локальной системе для доступа к службе приема обозреватель данных Azure. Решением является присоединение маркера идентификации к каждому принимающему сообщению. Токен включает проверку подлинности в основном канале. После этого подписанный маркер может быть проверен службой обозреватель данных Azure при получении сообщения приема.

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

### <a name="upload-data-to-the-azure-blob-container"></a>Отправка данных в контейнер больших двоичных объектов Azure

Этот шаг заключается в передаче локального файла в большой двоичный объект Azure, который будет передан для приема. Этот код использует пакет SDK службы хранилища Azure. Если зависимость невозможна, ее можно достичь с помощью [REST API службы BLOB-объектов Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api).

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

### <a name="compose-the-azure-data-explorer-ingestion-message"></a>Составление сообщения приема обозреватель данных Azure

Пакет NewtonSoft. JSON снова создаст допустимый запрос приема для определения целевой базы данных и таблицы и указывает на большой двоичный объект.
Сообщение будет отправлено в очередь Azure, которую прослушивает соответствующая служба Kusto Управление данными.

Ниже приведены некоторые моменты, которые следует учитывать.

* Этот запрос является минимальным числом для сообщения приема.

> [!NOTE]
> Маркер удостоверения является обязательным и должен быть частью объекта **AdditionalProperties** JSON.

* При необходимости также необходимо указать свойства Ксвмаппинг или Жсонмаппинг
* Дополнительные сведения см. в [статье предварительное создание сопоставления приема](../../management/create-ingestion-mapping-command.md).
* [Внутренняя структура сообщения приема](#ingestion-message-internal-structure) раздела содержит описание структуры сообщений приема

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

### <a name="post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue"></a>Публикация сообщения приема обозреватель данных Azure в очередь приема обозреватель данных Azure

Наконец, опубликуйте созданное вами сообщение в выбранную очередь приема, полученную из Azure обозреватель данных.

> [!NOTE]
> При использовании клиента хранилища .NET по умолчанию кодирует сообщение в Base64. Дополнительные сведения см. в разделе [Документация по хранилищу](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.encodemessage?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_EncodeMessage). Если вы не используете этот клиент, убедитесь, что содержимое сообщения правильно закодировано.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

### <a name="check-for-error-messages-from-the-azure-queue"></a>Проверка сообщений об ошибках из очереди Azure

После приема мы проверяем сообщения об ошибках из соответствующей очереди, в которую Управление данными записывает данные. Дополнительные сведения о структуре сообщений об ошибках см. в разделе [Структура сообщений об ошибках приема](#ingestion-failure-message-structure). 

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

## <a name="ingestion-messages---json-document-formats"></a>Сообщения приема — форматы документов JSON

### <a name="ingestion-message-internal-structure"></a>Внутренняя структура сообщения приема

Сообщение, которое служба Kusto Управление данными считывает из входной очереди Azure, является документом JSON в следующем формате.

```JSON
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
|BlobPath |Путь (URI) к большому двоичному объекту, включая ключ SAS, предоставляющий разрешения Azure обозреватель данных для чтения, записи и удаления. Требуются разрешения, чтобы служба Azure обозреватель данных могла удалить большой двоичный объект после завершения приема данных.|
|равдатасизе |Размер несжатых данных в байтах. Предоставление этого значения позволяет обозреватель данных Azure оптимизировать прием, потенциально выполнив статистическую обработку нескольких больших двоичных объектов. Это свойство является необязательным, но если оно не задано, Azure обозреватель данных будет обращаться к большому двоичному объекту только для получения размера |
|имя_базы_данных |Имя целевой базы данных |
|TableName |Имя целевой таблицы |
|ретаинблобонсукцесс |Если задано значение `true` , большой двоичный объект не будет удален после успешного завершения приема. Значение по умолчанию — `false`. |
|Формат |Формат несжатых данных |
|флушиммедиатели |Если задано значение `true` , любые статистические вычисления будут пропущены. Значение по умолчанию — `false`. |
|репортлевел |Уровень отчетов об успешном выполнении и ошибках: 0-сбоев, 1 — нет, 2 — все |
|репортмесод |Механизм создания отчетов: 0 — очередь, 1-Таблица |
|AdditionalProperties |Дополнительные свойства, такие как Теги |

### <a name="ingestion-failure-message-structure"></a>Структура сообщения об ошибке приема

Сообщение, которое Управление данными требуется считывать из входной очереди Azure, является документом JSON в следующем формате.

|Свойство. | Описание |
|---------|-------------
|Операции |Идентификатор операции (GUID), который можно использовать для отслеживания операции на стороне службы |
|База данных |Имя целевой базы данных |
|Таблица |Имя целевой таблицы |
|фаиледон |Метка времени сбоя |
|инжестионсаурцеид |GUID, определяющий блок данных, который обозреватель данных Azure не удалось принять |
|инжестионсаурцепас |Путь (URI) к блоку данных, который обозреватель данных Azure не удалось принять |
|Сведения |Сообщение об ошибке |
|ErrorCode |Код ошибки обозреватель данных Azure (см. все коды ошибок [здесь](kusto-ingest-client-errors.md#ingestion-error-codes)) |
|фаилурестатус |Указывает, является ли сбой постоянным или временным |
|RootActivityId |Идентификатор корреляции Azure обозреватель данных (GUID), который можно использовать для отслеживания операции на стороне службы. |
|оригинатесфромупдатеполици |Указывает, вызвана ли ошибка ошибочной [политикой обновления транзакций](../../management/updatepolicy.md) |
|шаулдретри | Указывает, может ли прием успешных попыток, если это так. |
