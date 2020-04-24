---
title: 做法資料內嵌但不 Kusto。內嵌程式庫-Azure 資料總管 |Microsoft Docs
description: 本文說明在 Azure 資料總管中，不含 Kusto 的做法資料內嵌程式庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9e4be7be65b0fe118a99835b24cd9d69ac5a531d
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108485"
---
# <a name="howto-data-ingestion-without-kustoingest-library"></a>做法資料內嵌但不 Kusto。內嵌程式庫

## <a name="when-to-consider-not-using-kustoingest-library"></a>為什麼要考慮不要使用 Kusto 程式庫？
一般來說，只要考慮將資料內嵌到 Kusto，就應該偏好使用 Kusto 程式庫。<BR>
如果這不是可行的選項（通常是因為作業系統條件約束），只有一個工作可以達到幾乎相同的功能。<BR>
本文說明如何在不依賴 Kusto 封裝的情況下，將**佇列**的內嵌執行 Kusto。

>**注意：** 下列程式碼是以 c # 撰寫，利用 Azure 儲存體 SDK、ADAL 驗證程式庫和 NewtonSoft JSON 封裝來簡化範例程式碼。<BR>如有需要，您可以將對應的程式碼取代為適當的[Azure 儲存體 REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)呼叫、 [non-.NET ADAL 封裝](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)和任何可用的 JSON 處理封裝。

## <a name="overview"></a>概觀
下列程式碼範例示範如何在不使用 Kusto 程式庫的情況下，將資料內嵌到 Kusto 中（透過 Kusto 資料管理 service）內嵌至。<BR>
如果完整的 .NET 因環境或其他限制而無法存取或無法使用，這可能會很有用。<BR>

> 本文針對生產等級管線（也稱為**佇列**內嵌，其對應的實體是[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)介面）處理建議的內嵌模式，以進行內嵌的功能。 在此模式中，用戶端程式代碼會藉由將內嵌通知訊息張貼至 Azure 佇列來與 Kusto 服務互動，其參考是從 Kusto 資料管理取得（也稱為 內嵌）服務。 與資料管理服務的互動必須使用**AAD**進行驗證。

此流程的大綱會在下面的程式碼範例中說明，並由下列內容組成：
1. 建立 Azure 儲存體用戶端，並將資料上傳至 blob
1. 取得用於存取 Kusto 內嵌服務的驗證權杖
2. 查詢 Kusto 的內嵌服務，以便取得：
    * 內嵌資源（佇列和 blob 容器）
    * 將新增至每個內嵌訊息的 Kusto 身分識別權杖
3. 將資料上傳至從（2）中的 Kusto 取得的其中一個 blob 容器上的 blob
4. 撰寫用於識別目標 DB 和資料表的內嵌訊息，並指向中的 blob （3）
5. 將我們在（4）中撰寫的內嵌訊息，張貼至從中的 Kusto 取得的其中一個內嵌佇列（2）
6. 捕獲服務在內嵌期間遇到的任何錯誤

後續各節將更詳細地說明每個步驟。

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

## <a name="1-obtain-authentication-evidence-from-aad"></a>1. 從 AAD 取得驗證辨識項
在此，我們會使用 ADAL 來取得 AAD 權杖，以存取 Kusto 資料管理服務，以便要求其輸入佇列。
如有需要，可在[非 Windows 平臺](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)上使用 ADAL。
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

## <a name="2-retrieve-kusto-ingestion-resources"></a>2. 取出 Kusto 的內嵌資源
這就是有趣的東西。 在此，我們會以手動方式將 HTTP POST 要求建立到 Kusto 資料管理服務，要求傳回內嵌資源。
這些包括 DM 服務正在接聽的佇列，以及用於資料上傳的 blob 容器。
資料管理服務會處理任何訊息，其中包含抵達其中一個佇列的內嵌要求。
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

## <a name="obtaining-kusto-identity-token"></a>取得 Kusto 身分識別權杖
授權資料內嵌的一個重要步驟是取得身分識別權杖，並將它附加至每個內嵌訊息。 由於內嵌訊息會透過非直接通道（Azure 佇列）遞交給 Kusto，因此無法執行頻外授權驗證。 身分識別權杖機制會發出 Kusto 簽署的識別辨識項，以便在收到內嵌訊息之後，由 Kusto 服務進行驗證。
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

## <a name="3-upload-data-to-azure-blob-container"></a>3. 將資料上傳至 Azure Blob 容器
此步驟是關於將本機檔案上傳至 Azure Blob，稍後將會針對內嵌進行傳遞。 這段程式碼會使用 Azure 儲存體 SDK，但不可能發生此相依性，您可以透過[Azure Blob 服務 REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api)達成相同的目的。
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

## <a name="4-compose-kusto-ingestion-message"></a>4. 撰寫 Kusto 的內嵌訊息
在這裡，我們再次使用 NewtonSoft 來撰寫有效的內嵌要求訊息，其將會張貼至適當的 Kusto 資料管理服務正在接聽的 Azure 佇列。
* 這是內嵌訊息的最小值。
* 請注意，該身分識別權杖是必要的，而且`AdditionalProperties`必須位於 JSON 物件中。
* 必要時， `CsvMapping`也必須`JsonMapping`提供或屬性
* 如需詳細資訊，請參閱[預先建立的內嵌對應文章](../../management/create-ingestion-mapping-command.md)
* [附錄 A](#appendix-a-ingestion-message-internal-structure)提供有關內嵌訊息結構的說明

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

## <a name="5-post-kusto-ingestion-message-to-kusto-ingestion-queue"></a>5. 將 Kusto 內嵌訊息張貼至 Kusto 的內嵌佇列
最後，deed 本身-只會將我們所建立的訊息張貼到我們選擇的佇列中。<BR>
注意：使用 .Net 儲存體用戶端時，它預設會將訊息編碼為 base64。 請參閱[儲存體](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage)檔。<BR>
如果您不是使用該用戶端，請務必適當地編碼訊息內容。

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

## <a name="6-pop-messages-from-an-azure-queue"></a>6. 來自 Azure 佇列的 Pop 訊息
內嵌之後，我們會使用這個方法，從 Kusto 資料管理服務所寫入的適當佇列中讀取任何失敗訊息。<BR>
[附錄 B](#appendix-b-ingestion-failure-message-structure)提供失敗訊息結構的說明

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

## <a name="appendix-a-ingestion-message-internal-structure"></a>附錄 A：內嵌訊息內部結構
Kusto 資料管理服務預期會從輸入 Azure 佇列讀取的訊息，是下列格式的 JSON 檔：

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


|屬性 | 描述 |
|---------|-------------|
|Id |訊息識別碼（GUID） |
|BlobPath |Blob URI，包括將 Kusto 許可權授與讀取/寫入/刪除它的 SAS 金鑰（如果 Kusto 是在完成內嵌資料後刪除 blob，則需要寫入/刪除許可權） |
|RawDataSize |未壓縮資料的大小（以位元組為單位）。 提供此值可讓 Kusto 將多個 blob 匯總在一起，以優化內嵌。 此屬性是選擇性的，但如果未提供，Kusto 只會存取 blob 以取得大小 |
|DatabaseName |目標資料庫名稱 |
|TableName |目標資料表名稱 |
|RetainBlobOnSuccess |如果設定為`true`，則一旦成功完成內嵌之後，將不會刪除 blob。 預設為 `false` |
|[格式] |未壓縮的資料格式 |
|FlushImmediately |如果設定為`true`，則會略過任何匯總。 預設為 `false` |
|ReportLevel |成功/錯誤報表層級： 0-失敗，1-無，2-全部 |
|ReportMethod |報告機制： 0-佇列，1-資料表 |
|AdditionalProperties |任何其他屬性（標記等等） |


## <a name="appendix-b-ingestion-failure-message-structure"></a>附錄 B：內嵌失敗訊息結構
Kusto 資料管理服務預期會從輸入 Azure 佇列讀取的下表訊息是下列格式的 JSON 檔：

|屬性 | 描述 |
|---------|-------------
|OperationId |可以用來追蹤服務端作業的作業識別碼（GUID） |
|資料庫 |目標資料庫名稱 |
|Table |目標資料表名稱 |
|FailedOn |失敗時間戳記 |
|IngestionSourceId |識別 Kusto 無法內嵌之資料區塊的 GUID |
|IngestionSourcePath |Kusto 無法內嵌之資料區塊的路徑（URI） |
|詳細資料 |失敗訊息 |
|ErrorCode |Kusto 錯誤碼（請參閱[這裡](kusto-ingest-client-errors.md#ingestion-error-codes)的所有錯誤碼） |
|FailureStatus |指出失敗是永久的還是暫時性的 |
|RootActivityId |可用於追蹤服務端作業的 Kusto 相互關聯識別碼（GUID） |
|OriginatesFromUpdatePolicy |指出失敗是否由錯誤的[交易式更新原則](../../management/updatepolicy.md)所造成 |
|ShouldRetry | 指出如已重試，內嵌是否可能成功 |