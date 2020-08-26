---
title: 使用 Azure 資料總管 .NET SDK 內嵌資料
description: 在本文中，您將瞭解如何使用 .NET SDK，將) 資料內嵌 (載入至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: vladikb
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: c6a544228d5527f1703567256bd3e824ddc0504a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872704"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-sdk"></a>使用 Azure 資料總管 .NET SDK 內嵌資料 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [節點](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 它提供兩個適用于 .NET 的用戶端程式庫：內嵌連結 [庫](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest/) 和 [資料連結庫](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data/)。 如需 .NET SDK 的詳細資訊，請參閱 [關於 .NET sdk](/azure/data-explorer/kusto/api/netfx/about-the-sdk)。
這些程式庫可讓您將資料內嵌 (載入) 至叢集，並從您的程式碼查詢資料。 在本文中，您會先在測試叢集中建立資料表和資料對應。 然後，您將叢集的擷取排入佇列並驗證結果。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

* [測試叢集和資料庫](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>安裝內嵌程式庫

```azurecli
Install-Package Microsoft.Azure.Kusto.Ingest
```

## <a name="add-authentication-and-construct-connection-string"></a>新增驗證和建立連接字串

### <a name="authentication"></a>驗證

為了驗證應用程式，Azure 資料總管 SDK 會使用您的 AAD 租使用者識別碼。 若要尋找您的租用戶識別碼，請使用下列 URL，並以您的網域取代 *YourDomain*。

```http
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果您的網域為 *contoso.com*，則 URL 會是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 按一下此 URL 來查看結果；第一行如下所示。 

```console
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

在此案例中，租用戶識別碼為 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。

此範例會使用互動式 AAD 使用者驗證來存取叢集。 您也可以使用 AAD 應用程式驗證搭配憑證或應用程式秘密。 `tenantId`在執行此程式碼之前，請務必為和設定正確的值 `clusterUri` 。 

Azure 資料總管 SDK 提供一個便利的方式，將驗證方法設定為連接字串的一部分。 如需 Azure 資料總管連接字串的完整檔，請參閱 [連接字串](kusto/api/connection-strings/kusto.md)。

> [!NOTE]
> SDK 的最新版本不支援 .NET Core 上的互動式 uer authentication。 如有需要，請改用 AAD 使用者名稱/密碼或應用程式驗證。

### <a name="construct-the-connection-string"></a>建構連接字串

現在您可以建立 Azure 資料總管連接字串。 您將在稍後的步驟中建立目的地資料表和對應。

```csharp
var tenantId = "<TenantId>";
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net/";

var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri).WithAadUserPromptAuthentication(tenantId);
```

## <a name="set-source-file-information"></a>設定來源檔案資訊

設定來源檔案的路徑。 本範例使用裝載於 Azure Blob 儲存體的範例檔案。 **StormEvents**範例資料集包含來自國家中心的氣象相關資料[，以取得環境資訊](https://www.ncdc.noaa.gov/stormevents/)。

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>在測試叢集上建立資料表

建立名為 `StormEvents` 的資料表，該資料表與 `StormEvents.csv` 檔案中的資料結構描述相符。

> [!TIP]
> 下列程式碼片段幾乎每次呼叫都會建立用戶端的實例。 這是為了讓每個程式碼片段個別執行。 在生產環境中，用戶端實例是可重新進入的，且應視需要保留。 每個 URI 的單一用戶端實例都已足夠，即使在使用多個資料庫時，也可以在命令層級上指定) 的 (資料庫。

```csharp
var databaseName = "<DatabaseName>";
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-ingestion-mapping"></a>定義擷取對應

將傳入的 CSV 資料對應到建立資料表時使用的資料行名稱。
在該資料表上布建 [CSV 資料行對應物件](kusto/management/create-ingestion-mapping-command.md) 。

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Csv,
            table,
            tableMapping,
            new[] {
                new ColumnMapping() { ColumnName = "StartTime", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "0" } } },
                new ColumnMapping() { ColumnName = "EndTime", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "1" } } },
                new ColumnMapping() { ColumnName = "EpisodeId", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "2" } } },
                new ColumnMapping() { ColumnName = "EventId", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "3" } } },
                new ColumnMapping() { ColumnName = "State", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "4" } } },
                new ColumnMapping() { ColumnName = "EventType", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "5" } } },
                new ColumnMapping() { ColumnName = "InjuriesDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "6" } } },
                new ColumnMapping() { ColumnName = "InjuriesIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "7" } } },
                new ColumnMapping() { ColumnName = "DeathsDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "8" } } },
                new ColumnMapping() { ColumnName = "DeathsIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "9" } } },
                new ColumnMapping() { ColumnName = "DamageProperty", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "10" } } },
                new ColumnMapping() { ColumnName = "DamageCrops", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "11" } } },
                new ColumnMapping() { ColumnName = "Source", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "12" } } },
                new ColumnMapping() { ColumnName = "BeginLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "13" } } },
                new ColumnMapping() { ColumnName = "EndLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "14" } } },
                new ColumnMapping() { ColumnName = "BeginLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "15" } } },
                new ColumnMapping() { ColumnName = "BeginLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "16" } } },
                new ColumnMapping() { ColumnName = "EndLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "17" } } },
                new ColumnMapping() { ColumnName = "EndLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "18" } } },
                new ColumnMapping() { ColumnName = "EpisodeNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "19" } } },
                new ColumnMapping() { ColumnName = "EventNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "20" } } },
                new ColumnMapping() { ColumnName = "StormSummary", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "21" } } }
        });

    kustoClient.ExecuteControlCommand(databaseName, command);
}
```

## <a name="define-batching-policy-for-your-table"></a>定義資料表的批次處理原則

Azure 資料總管內嵌會執行傳入資料的批次處理，以針對資料分區大小進行優化。 此程式是由內嵌 [批次處理原則](kusto/management/batchingpolicy.md) 所控制，並可透過內嵌 [批次處理原則控制命令](kusto/management/batching-policy.md)來修改。 使用此原則可減少緩慢抵達資料的延遲。

```kusto
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableAlterIngestionBatchingPolicyCommand(
        databaseName,
        table,
        new IngestionBatchingPolicy(maximumBatchingTimeSpan: TimeSpan.FromSeconds(10.0), maximumNumberOfItems: 100, maximumRawDataSizeMB: 1024));

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>將訊息排入佇列以供擷取

將訊息放入佇列，以從 Blob 儲存體提取資料，並將該資料內嵌至 Azure 資料總管。 與 Azure 資料總管叢集的資料內嵌端點建立連線，並建立另一個用戶端來使用該端點。 

> [!TIP]
> 下列程式碼片段幾乎每次呼叫都會建立用戶端的實例。 這是為了讓每個程式碼片段個別執行。 在生產環境中，用戶端實例是可重新進入的，且應視需要保留。 每個 URI 的單一用戶端實例都已足夠，即使在使用多個資料庫時，也可以在命令層級上指定) 的 (資料庫。

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net";
var ingestConnectionStringBuilder = new KustoConnectionStringBuilder(ingestUri).WithAadUserPromptAuthentication(tenantId);

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping,
                IngestionMappingKind = IngestionMappingKind.Csv
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath, ingestionProperties: properties).GetAwaiter().GetResult();
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>驗證已將資料內嵌至資料表

等候五到十分鐘，讓佇列的內嵌排程內嵌，並將資料載入 Azure 資料總管中。 然後執行下列程式碼，以取得 `StormEvents` 資料表中的記錄計數。

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(databaseName, query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>執行疑難排解查詢

登入 [https://dataexplorer.azure.com](https://dataexplorer.azure.com) 並聯機至您的叢集。 在資料庫中執行下列命令，以查看最後四個小時是否有任何擷取失敗。 先取代資料庫名稱，再執行。

```kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

執行下列命令，以檢視最後四個小時內的所有擷取作業狀態。 先取代資料庫名稱，再執行。

```kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>清除資源

如果您打算遵循其他文章，請保留您建立的資源。 否則，請在資料庫中執行下列命令，來清除 `StormEvents` 資料表。

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>後續步驟

* [撰寫查詢](write-queries.md)
