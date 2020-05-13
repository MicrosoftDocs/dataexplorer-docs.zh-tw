---
title: Kusto。內嵌內嵌狀態報表-Azure 資料總管
description: 本文說明 Azure 資料總管中的 Kusto 內嵌狀態報表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 76ae07e2e7bdbb15900385b1e2feab0c9ff97d01
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373621"
---
# <a name="kustoingest-ingestion-status-reporting"></a>Kusto。內嵌內嵌狀態報表

本文說明如何使用[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)功能來追蹤內嵌要求的狀態。

## <a name="description-classes"></a>描述類別

這些描述類別包含要內嵌之來源資料的重要詳細資訊，而且應該用於內嵌作業。 

* SourceDescription
* DataReaderDescription
* StreamDescription
* FileDescription
* BlobDescription

類別全都衍生自抽象類別別 `SourceDescription` ，而且是用來具現化每個資料來源的唯一識別碼。 接著，每個識別碼會用於狀態追蹤，並會顯示在與相關作業相關的所有報表、追蹤和例外狀況中。

### <a name="class-sourcedescription"></a>類別 SourceDescription

大型資料集會分割成1GB 區塊，而每個元件會分別內嵌。 然後，相同的 SourceId 會套用至源自相同資料集的所有內嵌作業。   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>類別 DataReaderDescription

```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>類別 StreamDescription

```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>類別 FileDescription

```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>類別 BlobDescription

```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>內嵌結果標記法

### <a name="interface-ikustoingestionresult"></a>介面 IKustoIngestionResult

這個介面會捕獲單一佇列內嵌作業的結果，而且可以由抓取 `SourceId` 。

```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>類別 IngestionStatus

IngestionStatus 包含單一內嵌作業的完整狀態。

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

### <a name="status-enumeration"></a>狀態列舉

|值              |意義                                                                                     |暫存/永久
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
|Pending            |根據內嵌作業的結果，在內嵌過程中，此值可能會變更 |暫存|
|成功          |已成功內嵌資料                                                              |持續性| 
|失敗             |內嵌失敗                                                                                     |持續性|
|已排入佇列             |已將資料排入佇列以進行內嵌                                                               |持續性|
|已略過            |未提供任何資料，且已略過內嵌操作                                            |持續性|
|PartiallySucceeded |已成功內嵌部分資料，但有些失敗                                        |持續性|

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>追蹤內嵌狀態（KustoQueuedIngestClient）

[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)是「火災又忘」的用戶端。 用戶端上的內嵌作業會將訊息張貼到 Azure 佇列來結束。 張貼之後，用戶端作業就完成了。 針對用戶端使用者的便利性，KustoQueuedIngestClient 提供一個機制來追蹤個別的內嵌狀態。 此機制不適用於高輸送量內嵌管線上的大量使用量。 當速率相對較低且追蹤需求為嚴格時，此機制適用于精確內嵌。

> [!WARNING]
> 您應該避免針對大型磁片區資料流程的每個內嵌要求開啟正面通知，因為這會對基礎 xStore 資源造成極大的負載，而這可能會導致內嵌延遲增加，甚至完成叢集非回應能力。

下列屬性（在[KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)上設定）會控制內嵌成功或失敗通知的層級和傳輸。

### <a name="ingestionreportlevel-enumeration"></a>IngestionReportLevel 列舉

```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>IngestionReportMethod 列舉

```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

若要追蹤您的內嵌狀態，請將下列內容提供給您執行內嵌作業所使用的[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ：
1.  將 `IngestionReportLevel` 屬性設定為所需的報表層級。 `FailuresOnly`（也就是預設值）或 `FailuresAndSuccesses` 。
設定為時 `None` ，將不會在內嵌的結尾回報任何內容。
1.  指定 `IngestionReportMethod`  -  `Queue` 、 `Table` 或 `both` 。

使用範例可以在 Kusto 的 [[範例](kusto-ingest-client-examples.md)] 頁面上找到。

### <a name="ingestion-status-in-the-azure-table"></a>Azure 資料表中的內嵌狀態

`IKustoIngestionResult`從每個內嵌作業傳回的介面包含可用來查詢內嵌狀態的函數。
特別注意 `Status` 傳回物件的屬性 `IngestionStatus` ：
* `Pending`表示來源已排入佇列以供內嵌，尚未更新。 再次使用函數來查詢來源的狀態
* `Succeeded`表示已成功內嵌來源
* `Failed`指出無法內嵌來源

> [!NOTE]
> 取得 `Queued` 狀態表示 `IngestionReportMethod` 已保留其預設值「佇列」。 這是永久的狀態，重新叫用或函式時 `GetIngestionStatusBySourceId` `GetIngestionStatusCollection` ，一律會產生相同的「已排入佇列」狀態。
> 若要檢查 Azure 資料表中的內嵌狀態，在內嵌之前，請確認 `IngestionReportMethod` [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)的屬性已設定為 `Table` 。 如果您也想要將內嵌狀態回報給佇列，請將狀態設定為 `QueueAndTable` 。

### <a name="ingestion-status-in-azure-queue"></a>Azure 佇列中的內嵌狀態

這些 `IKustoIngestionResult` 方法僅適用于檢查 Azure 資料表中的狀態。 若要查詢向 Azure 佇列回報的狀態，請使用下列[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)方法。

|方法                                  |目的     |
|----------------------------------------|------------|
|PeekTopIngestionFailures                |非同步方法，傳回因要求的訊息限制而尚未捨棄的最早內嵌失敗相關資訊 |
|GetAndDiscardTopIngestionFailures       |非同步方法，會傳回並捨棄因為要求的訊息限制而尚未捨棄的最早內嵌失敗 |
|GetAndDiscardTopIngestionSuccesses      |非同步方法會傳回並捨棄因要求的訊息限制而尚未捨棄的最早內嵌成功。 只有當設定為時，這個方法才會相關 `IngestionReportLevel``FailuresAndSuccesses` |

### <a name="ingestion-failures-retrieved-from-the-azure-queue"></a>從 Azure 佇列取出的內嵌失敗

內嵌失敗會以 `IngestionFailure` 物件表示，其中包含有關失敗的實用資訊。

|屬性                      |意義     |
|------------------------------|------------|
|資料庫 & 資料表              |預期的資料庫和資料表名稱 |
|IngestionSourcePath           |內嵌 blob 的路徑。 如果檔案是內嵌，則會包含原始檔案名稱。 如果 DataReader 為內嵌，則為隨機 |
|FailureStatus                 |`Permanent`（將不會執行重試）、 `Transient` （將執行重試），或 `Exhausted` （多次重試也失敗） |
|OperationId & RootActivityId  |內嵌的作業識別碼和 RootActivity 識別碼（適用于進一步疑難排解） |
|FailedOn                      |失敗的 UTC 時間。 會大於呼叫內嵌方法的時間，因為資料會在執行內嵌之前匯總 |
|詳細資料                       |關於失敗的其他詳細資料（如果有的話） |
|ErrorCode                     |`IngestionErrorCode`列舉，代表發生失敗時的內嵌錯誤碼 |
