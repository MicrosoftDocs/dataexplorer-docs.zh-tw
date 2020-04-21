---
title: Kusto.Ingest 參考 - 攝取狀態報告 - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 Kusto.ingest 參考 - 引入狀態報告。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1a3eed1db0ec7d3dd4bc83c0a65f342020b2a387
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523710"
---
# <a name="kustoingest-reference---ingestion-status-reporting"></a>Kusto.Ingest 參考 - 攝取狀態報告
本文介紹如何使用[IKustoQueueingestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)功能來追蹤引入請求的狀態。

## <a name="sourcedescription-datareaderdescription-streamdescription-filedescription-and-blobdescription"></a>來源描述、資料閱讀器描述、串流描述、檔案描述和 Blob 描述
這些不同的描述類封裝了有關要引入的源數據的重要詳細資訊,並且可能甚至應該提供給引入操作。
所有這些類都派生自抽象類`SourceDescription`,用於實例化每個數據源的唯一標識符。
提供的標識碼用於後續操作狀態追蹤,並將顯示在與相應操作相關的所有報表、追蹤和異常中。

### <a name="class-sourcedescription"></a>類別描述
>引入大型資料集(例如,資料閱讀器超過 1GB) 時, 資料將拆分為 1GB 塊並單獨引入。<BR>在這種情況下,相同的 SourceId 將應用於源自同一數據集的所有引入操作。   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>類別資料閱讀器描述
```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>類別描述
```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>類別描述
```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>類別 Blob 描述
```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>引入結果表示

### <a name="interface-ikustoingestionresult"></a>介面 IKustoings 結果
此介面捕獲單個排隊引入操作的結果,並允許通過`SourceId`檢索它。
```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>類別引入狀態
引入狀態封裝單個引入操作的完整狀態。
```csharp
public class IngestionStatus
{
    // The ingestion status returns from the service. Status remains 'Pending' during the ingestion process and
    // is updated by the service once the ingestion completes. When <see cref="IngestionReportMethod"/> is set to 'Queue' the ingestion status
    // will always be 'Queued' and the caller needs to query the report queues for ingestion status, as configured. To query statuses that were
    // reported to queue, see: <see href="https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-status#ingestion-status-in-azure-queue"/>.
    // When <see cref="IngestionReportMethod"/> is set to 'Table', call <see cref="IKustoIngestionResult.GetIngestionStatusBySourceId"/> or
    // <see cref="IKustoIngestionResult.GetIngestionStatusCollection"/> to retrieve the most recent ingestion status.
    public Status Status { get; set; }
    // A unique identifier representing the ingested source. Can be supplied during the ingestion execution.
    public Guid IngestionSourceId { get; set; }
    // The URI of the blob, potentially including the secret needed to access the blob.
    // This can be a filesystem URI (on-premises deployments only), or an Azure Blob Storage URI (including a SAS key or a semicolon followed by the account key)
    public string IngestionSourcePath { get; set; }
    // The name of the database holding the target table.
    public string Database { get; set; }
    // The name of the target table into which the data will be ingested.
    public string Table { get; set; }
    // The last updated time of the ingestion status.
    public DateTime UpdatedOn { get; set; }
    // The ingestion's operation Id.
    public Guid OperationId { get; set; }
    // The ingestion operation activity Id.
    public Guid ActivityId { get; set; }
    // In case of a failure - indicates the failure's error code.
    public IngestionErrorCode ErrorCode { get; set; }
    // In case of a failure - indicates the failure's status.
    public FailureStatus FailureStatus { get; set; }
    // In case of a failure - indicates the failure's details.
    public string Details { get; set; }
    // In case of a failure - indicates whether or not the failures originate from an Update Policy.
    public bool OriginatesFromUpdatePolicy { get; set; }
}
```

### <a name="status-enumeration"></a>狀態枚舉
|值 |意義 |
|------------|------------|
|Pending |臨時。 根據攝入操作的結果,在攝入過程中可能會發生變化 |
|成功 |永久。 他的數據已成功攝用 |
|失敗 |永久。 攝入失敗 |
|已排入佇列 |永久。 資料已排隊等待攝取 |
|已略過 |永久。 未提供任何資料,並且跳過了引入操作 |
|部分成功 |永久。 部分數據已成功攝用,而某些數據失敗 |

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>追蹤引入狀態 (庫斯特排機客戶)
[IKustoQueueingestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)是一個"即用即用"用戶端 - 用戶端上的引入操作通過在將消息發佈到 Azure 佇列結束,之後將完成用戶端作業。 為方便用戶端使用者,KustoQueuedingestClient 提供了一種用於追蹤單個引入狀態的機制。 這不適用於高通量引入管道的大規模使用,而是在速率相對較低且跟蹤要求非常嚴格時用於"精確"引入。

> [!WARNING]
> 應避免為大容量數據流的每個引入請求打開正通知,因為這會對底層的 xStore 資源造成極大的負載,>这可能导致引入延迟增加,甚至完全群集無回應性。


以下屬性(在[KustoQueuedInginge 屬性](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)上設定)控制接收成功或失敗通知的層級和傳輸:

### <a name="ingestionreportlevel-enumeration"></a>引入報告等級枚舉
```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>引入報告方法列舉
```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

為了能夠跟蹤您的攝入狀態,請確保向執行攝取操作的[IKustoQueueingestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)提供以下內容:
1.  將`IngestionReportLevel`屬性設定為所需的報告級別 - 僅限故障(預設值)或失敗和成功。
當設置為"無"時,在攝入結束時不會報告任何內容。
2.  指定所需的`IngestionReportMethod`- 佇列、表或兩者。

可以使用範例在[Kusto.Ingest 範例](kusto-ingest-client-examples.md)頁面上找到。

### <a name="ingestion-status-in-azure-table"></a>Azure 表格引入狀態
從`IKustoIngestionResult`每個引入操作返回的介面包含可用於查詢引入狀態的函數。
特別注意傳`Status``IngestionStatus`回 的物件的屬性:
* `Pending`指示源已排隊等待引入,並且尚未更新;再次使用 函式查詢來源的狀態
* `Succeeded`指示來源已成功引入
* `Failed`指示來源未被引入

>請注意,獲取狀態`Queued``IngestionReportMethod`表示 保留的預設值為"佇列"。 這是一個永久狀態,重新調用"獲取狀態由來源Id"或"獲取狀態收集"函數將始終導致相同的"排隊"狀態。<BR>為了能夠檢查 Azure 表中的引入狀態,`IngestionReportMethod`請在引入[KustoQueueinge](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)屬性`Table`的屬性設置為`QueueAndTable`之前進行驗證(或者 如果您還希望將引入狀態報告到佇列)。

### <a name="ingestion-status-in-azure-queue"></a>Azure 佇列中引入狀態
這些方法`IKustoIngestionResult`僅與檢查 Azure 表中的狀態相關。 要查詢已報告到 Azure 佇列的狀態,請使用以下[IKustoQueueingest 用戶端](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)的方法:

|方法 |目的 |
|------------|------------|
|窺視上點失敗 |根據請求的消息限制返回有關未丟棄的最早引入失敗的資訊的非同步方法 |
|取得與放棄上置失敗 |根據請求的消息限制返回並丟棄未丟棄的最早引入故障的非同步方法 |
|獲得和放棄的上流成功 |根據請求的消息限制,返回並丟棄未丟棄的最早引入成功(僅設置為`IngestionReportLevel``FailuresAndSuccesses` |


### <a name="ingestion-failures-retrieved-from-azure-queue"></a>從 Azure 佇列中取出的失敗
引入失敗由`IngestionFailure`包含有關故障的有用資訊的物件表示:

|屬性 |意義 |
|------------|------------|
|資料庫&表 |預期的資料庫與表格名稱 |
|引入來源路徑 |引入的 blob 的路徑。 將在檔被引入的情況下包含原始檔名。 在資料閱讀器被引入的情況下,將是隨機的 |
|失敗狀態 |`Permanent`( 不執行重試)、(`Transient`將執行重試`Exhausted`)或 (幾次重試也失敗) |
|操作代碼 &根活动代碼 |引入的操作 ID 和 RootActivity ID(可用於進一步故障排除) |
|失敗開啟 |故障的 UTC 時間。 將大於調用引入方法的時間,因為在執行引入之前正在聚合數據 |
|詳細資料 |有關故障的其他詳細資訊(如果存在) |
|ErrorCode |`IngestionErrorCode`Entl 表示引入錯誤代碼,以防失敗發生|