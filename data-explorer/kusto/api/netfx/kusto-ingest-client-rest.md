---
title: 不含內嵌程式庫的 Kusto 資料內嵌-Azure 資料總管
description: 本文說明在 Azure 資料總管中，不含 Kusto 的做法資料內嵌程式庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: eb13b53ba5f6785c79aaa586de50478074901c8d
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85901919"
---
# <a name="ingestion-without-kustoingest-library"></a>不 Kusto 內嵌程式庫的內嵌

Kusto 是將資料內嵌至 Azure 資料總管慣用的程式庫。 不過，您仍然可以達成幾乎相同的功能，而不需依賴 Kusto 的封裝。
本文將為您示範如何使用適用于生產等級管線的 Azure 資料總管的*佇列*內嵌。

> [!NOTE]
> 下列程式碼是以 c # 撰寫，並利用 Azure 儲存體 SDK、ADAL 驗證程式庫和封裝上的 NewtonSoft.JS來簡化範例程式碼。 如有需要，您可以將對應的程式碼取代為適當的[Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)呼叫、 [non-.NET ADAL 封裝](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)，以及任何可用的 JSON 處理封裝。

本文會處理建議的內嵌模式。 針對 Kusto 程式庫，其對應的實體是[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)介面。 在這裡，用戶端程式代碼會藉由將內嵌通知訊息張貼至 Azure 佇列，來與 Azure 資料總管服務互動。 訊息的參考是從 Kusto 資料管理（也稱為內嵌）服務取得。 與服務的互動必須使用 Azure Active Directory （Azure AD）進行驗證。

下列程式碼顯示 Kusto 資料管理服務如何處理佇列的資料內嵌，而不需要使用 Kusto 程式庫。 如果完整的 .NET 因環境或其他限制而無法存取或無法使用，此範例可能會很有用。

程式碼包含建立 Azure 儲存體用戶端並將資料上傳至 blob 的步驟。
在範例程式碼之後，會更詳細地說明每個步驟。

1. [取得驗證權杖以存取 Azure 資料總管的內嵌服務](#obtain-authentication-evidence-from-azure-ad)
1. 查詢 Azure 資料總管的內嵌服務，以取得：
    * [內嵌資源（佇列和 blob 容器）](#retrieve-azure-data-explorer-ingestion-resources)
    * [將新增至每個內嵌訊息的 Kusto 身分識別權杖](#obtain-a-kusto-identity-token)
1. [將資料上傳至從（2）中的 Kusto 取得的其中一個 blob 容器上的 blob](#upload-data-to-the-azure-blob-container)
1. [撰寫可識別目標資料庫和資料表，並從指向 blob 的內嵌訊息（3）](#compose-the-azure-data-explorer-ingestion-message)
1. [將我們在（4）中撰寫的內嵌訊息，張貼至從中的 Azure 資料總管取得的內建佇列（2）](#post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue)**
1. [抓取服務在內嵌期間發現的任何錯誤](#check-for-error-messages-from-the-azure-queue)

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

## <a name="using-queued-ingestion-to-azure-data-explorer-for-production-grade-pipelines"></a>針對生產等級管線使用已佇列的內嵌至 Azure 資料總管

### <a name="obtain-authentication-evidence-from-azure-ad"></a>從 Azure AD 取得驗證辨識項

在此，我們會使用 ADAL 來取得 Azure AD token，以存取 Kusto 資料管理服務並要求其輸入佇列。
如有需要，可在[非 Windows 平臺](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)上使用 ADAL。

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

### <a name="retrieve-azure-data-explorer-ingestion-resources"></a>取得 Azure 資料總管的內嵌資源

手動建立資料管理服務的 HTTP POST 要求，並要求傳回內嵌資源。 這些資源包括 DM 服務正在接聽的佇列，以及用於資料上傳的 blob 容器。
資料管理服務會處理任何訊息，其中包含抵達其中一個佇列的內嵌要求。

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

### <a name="obtain-a-kusto-identity-token"></a>取得 Kusto 身分識別權杖

內嵌訊息會透過非直接通道（Azure 佇列）遞交給 Azure 資料總管，因此不可能進行頻外授權驗證以存取 Azure 資料總管的內嵌服務。 解決方案是將身分識別權杖附加到每個內嵌訊息。 權杖會啟用頻內授權驗證。 此帶正負號的權杖會在收到內嵌訊息時，由 Azure 資料總管服務進行驗證。

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

### <a name="upload-data-to-the-azure-blob-container"></a>將資料上傳至 Azure Blob 容器

此步驟是關於將本機檔案上傳至 Azure Blob，而該檔案將會針對內嵌進行傳遞。 這段程式碼會使用 Azure 儲存體 SDK。 如果無法進行相依性，可以使用[Azure Blob 服務 REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api)來達成相關性。

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

### <a name="compose-the-azure-data-explorer-ingestion-message"></a>撰寫 Azure 資料總管的內嵌訊息

封裝上的 NewtonSoft.JS將再次撰寫有效的內嵌要求，以識別目標資料庫和資料表，並指向 blob。
訊息將會張貼到相關的 Kusto 資料管理服務正在接聽的 Azure 佇列。

以下是一些要考慮的重點。

* 此要求是內嵌訊息的最小值。

> [!NOTE]
> 身分識別權杖是必要的，而且必須是**AdditionalProperties** JSON 物件的一部分。

* 必要時，也必須提供 CsvMapping 或 JsonMapping 屬性
* 如需詳細資訊，請參閱[預先建立的內嵌對應文章](../../management/create-ingestion-mapping-command.md)。
* 區段內嵌[訊息內部結構](#ingestion-message-internal-structure)提供內建訊息結構的說明

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
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef),
                                            // Data is in JSON format
                                            new JProperty("format", "json")));
    return message.ToString();
}
```

### <a name="post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue"></a>將 Azure 資料總管內嵌訊息張貼至 Azure 資料總管的內嵌佇列

最後，將您所建立的訊息張貼到您從 Azure 資料總管取得的所選內嵌佇列。

> [!NOTE]
> .Net 儲存體用戶端在使用時，預設會將訊息編碼為 base64。 如需詳細資訊，請參閱[儲存體](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage?view=azure-dotnet-legacy#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_EncodeMessage)檔。如果您不是使用該用戶端，請務必適當地編碼訊息內容。

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

### <a name="check-for-error-messages-from-the-azure-queue"></a>檢查來自 Azure 佇列的錯誤訊息

在內嵌之後，我們會檢查來自資料管理寫入的相關佇列中的失敗訊息。 如需失敗訊息結構的詳細資訊，請參閱內嵌[失敗訊息結構](#ingestion-failure-message-structure)。 

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

## <a name="ingestion-messages---json-document-formats"></a>內嵌訊息-JSON 檔案格式

### <a name="ingestion-message-internal-structure"></a>內嵌訊息內部結構

Kusto 資料管理服務預期要從輸入 Azure 佇列讀取的訊息是下列格式的 JSON 檔。

```JSON
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```

|屬性 | 說明 |
|---------|-------------|
|Id |訊息識別碼（GUID） |
|BlobPath |Blob 的路徑（URI），包括授與 Azure 資料總管許可權以讀取/寫入/刪除它的 SAS 金鑰。 需要許可權，才能讓 Azure 資料總管在內嵌資料完成後刪除 blob|
|RawDataSize |未壓縮資料的大小（以位元組為單位）。 提供此值可讓 Azure 資料總管藉由可能匯總多個 blob 來優化內嵌。 此屬性是選擇性的，但如果未指定，Azure 資料總管只會存取 blob 以取得大小 |
|DatabaseName |目標資料庫名稱 |
|TableName |目標資料表名稱 |
|RetainBlobOnSuccess |如果設定為 `true` ，則一旦成功完成內嵌之後，將不會刪除 blob。 預設為 `false` |
|FlushImmediately |如果設定為 `true` ，則會略過任何匯總。 預設為 `false` |
|ReportLevel |成功/錯誤報表層級： 0-失敗，1-無，2-全部 |
|ReportMethod |報告機制： 0-佇列，1-資料表 |
|AdditionalProperties |其他屬性，例如 `format` 、 `tags` 和 `creationTime` 。 如需詳細資訊，請參閱[資料內嵌屬性](../../../ingestion-properties.md)。|

### <a name="ingestion-failure-message-structure"></a>內嵌失敗訊息結構

資料管理預期要從輸入 Azure 佇列讀取的訊息是下列格式的 JSON 檔。

|屬性 | 說明 |
|---------|-------------
|OperationId |可以用來追蹤服務端作業的作業識別碼（GUID） |
|資料庫 |目標資料庫名稱 |
|Table |目標資料表名稱 |
|FailedOn |失敗時間戳記 |
|IngestionSourceId |識別 Azure 資料總管無法內嵌之資料區塊的 GUID |
|IngestionSourcePath |Azure 資料總管無法內嵌的資料區塊路徑（URI） |
|詳細資料 |失敗訊息 |
|ErrorCode |Azure 資料總管錯誤碼（請參閱[這裡](kusto-ingest-client-errors.md#ingestion-error-codes)的所有錯誤碼） |
|FailureStatus |指出失敗是永久的還是暫時性的 |
|RootActivityId |可用於追蹤服務端作業的 Azure 資料總管相互關聯識別碼（GUID） |
|OriginatesFromUpdatePolicy |指出失敗是否由錯誤的[交易式更新原則](../../management/updatepolicy.md)所造成 |
|ShouldRetry | 指出如已重試，內嵌是否可以成功 |
