---
title: 連續資料匯出-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的連續資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/27/2020
ms.openlocfilehash: 7abcead19e0306853bc6a585a41b5b79657a6842
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617709"
---
# <a name="continuous-data-export"></a>連續資料匯出

連續將資料從 Kusto 匯出到[外部資料表](../externaltables.md)。 外部資料表會定義目的地（例如 Azure Blob 儲存體）和匯出資料的架構。 匯出的資料是由定期執行的查詢所定義。 結果會儲存在外部資料表中。 此程式可保證所有記錄都是以「剛好一次」的方式匯出（不包括維度資料表，其中所有的記錄都會在所有執行中評估）。 

連續資料匯出會要求您[建立外部資料表](../externaltables.md#create-or-alter-external-table)，然後建立指向外部資料表[的連續匯出定義](#create-or-alter-continuous-export)。 

> [!NOTE] 
> * Kusto 不支援在連續匯出建立之前，匯出內嵌的歷程記錄（做為連續匯出的一部分）。 您可以使用（非連續）[匯出命令](export-data-to-an-external-table.md)分別匯出歷程記錄。 如需詳細資訊，請參閱[匯出歷程記錄資料](#exporting-historical-data)。 
> * 「連續匯出」不適用於使用串流內嵌的資料內嵌。 
> * 目前，無法在啟用[資料列層級安全性原則](../../management/rowlevelsecuritypolicy.md)的資料表上設定連續匯出。
> * 在其`impersonate` [連接字串](../../api/connection-strings/storage.md)中，外部資料表不支援連續匯出。
 
## <a name="notes"></a>注意

* *僅*針對 [[顯示匯出](#show-continuous-export-exported-artifacts)的成品] 命令所報告的檔案，保證「只一次」匯出。 
「連續匯出」*不*保證每一筆記錄只會寫入至外部資料表一次。 如果在匯出開始之後發生失敗，而且某些成品已經寫入外部資料表，則外部資料表_可能會_包含重複專案（或甚至是損毀的檔案，以防在完成之前中止寫入作業）。 在這種情況下，成品不會從外部資料表中刪除，但*不*會在 [[顯示匯出](#show-continuous-export-exported-artifacts)的成品] 命令中回報。 使用已匯出的檔案， `show exported artifacts command`並保證不會有重複專案（當然也不會損毀）。
* 若要保證「剛好一次」匯出，連續匯出會使用[資料庫資料指標](../databasecursor.md)。 
因此，必須在查詢中所參考的所有資料表上啟用[IngestionTime 原則](../ingestiontime-policy.md)，而該查詢應在匯出中以「剛好一次」處理。 預設會在所有新建立的資料表上啟用此原則。
* 匯出查詢的輸出架構*必須*符合您匯出之外部資料表的架構。 
* 連續匯出不支援跨資料庫/叢集呼叫。
* 「連續匯出」會根據為其設定的時間週期執行。 此間隔的建議值至少為數分鐘，視您願意接受的延遲而定。 「連續匯出」*並不是*為了持續串流資料而設計的 Kusto。 它會以分散式模式執行，其中所有節點會同時匯出。 如果每次執行所查詢的資料範圍很小，則連續匯出的輸出會是許多小型成品（此數目取決於叢集中的節點數目）。 
* 可以同時執行的匯出作業數目受限於叢集的資料匯出容量（請參閱[節流](../../management/capacitypolicy.md#throttling)）。 如果叢集沒有足夠的容量來處理所有連續匯出，有些會開始延遲。 
 
* 根據預設，匯出查詢中參考的所有資料表都會假設為[事實資料表](../../concepts/fact-and-dimension-tables.md)。 
因此，它們的*範圍*是資料庫資料指標。 匯出查詢中包含的記錄只是自上一次匯出執行後才聯結的記錄。 
匯出查詢可能包含[維度](../../concepts/fact-and-dimension-tables.md)資料表，其中維度資料表的*所有*記錄都包含在*所有*匯出查詢中。 
    * 在連續匯出的事實和維度資料表之間使用聯結時，您必須記住，事實資料表中的記錄只會處理一次-如果在某些索引鍵的維度資料表遺漏記錄時執行匯出，則會遺漏個別索引鍵的記錄，或在匯出的檔案中包含維度資料行的 null 值（視查詢是否使用內部或外部聯結而定）。 連續匯出定義中的 forcedLatency 屬性在這類情況下很有用，因為事實和維度資料表會在同一時間內嵌（用於比對記錄）。
    * 不支援只對維度資料表進行連續匯出。 匯出查詢必須包含至少一個事實資料表。
    * 語法會明確宣告哪些資料表已設定範圍（事實），而不是範圍（維度）。 如需`over`詳細資訊，請參閱[create 命令](#create-or-alter-continuous-export)中的參數。

* 在每個連續匯出反復專案中匯出的檔案數目，視外部資料表的分割方式而定。 如需進一步資訊，請參閱[匯出至外部資料表命令](export-data-to-an-external-table.md)中的附注一節。 請注意，每個連續匯出反復專案一律會寫入*新*檔案，而且永遠不會附加至現有檔案。 因此，匯出的檔案數目也取決於連續匯出的執行頻率（`intervalBetweenRuns`參數）。

所有連續匯出命令都需要[資料庫系統管理員許可權](../access-control/role-based-authorization.md)。

## <a name="create-or-alter-continuous-export"></a>建立或改變連續匯出

**語法：**

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*， *T2* `)`] <br>
`to``table` *ExternalTableName* <br> [ `with` `(` *PropertyName* PropertyName `=` * *PropertyValue`,`.。。`)`]<br>
\<| *查詢*

**屬性**：

| 屬性             | 類型     | 描述   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | 連續匯出的名稱。 名稱在資料庫內必須是唯一的，而且可用來定期執行連續匯出。      |
| ExternalTableName    | String   | 要匯出的[外部資料表](../externaltables.md)名稱。  |
| 查詢                | String   | 要匯出的查詢。  |
| over （T1，T2）        | String   | 查詢中的選擇性事實資料表清單（以逗號分隔）。 如果未指定，查詢中參考的所有資料表都會假設為事實資料表。 如果指定，則*不*在此清單中的資料表會被視為維度資料表，而且不會設定範圍（所有記錄都會參與所有匯出）。 如需詳細資訊，請參閱[附注一節](#notes)。 |
| intervalBetweenRuns  | Timespan | 連續匯出執行之間的時間範圍。 必須大於1分鐘。   |
| forcedLatency        | Timespan | 一段選擇性的時間，可將查詢限制為只在這段期間內嵌的記錄（相對於目前時間）。 例如，如果查詢執行一些匯總/聯結，而您想要確保所有相關記錄都已內嵌，然後才執行匯出，這會很有用。

除了上述以外，連續匯出 create 命令支援 [[匯出至外部資料表] 命令](export-data-to-an-external-table.md)所支援的所有屬性。 

**範例：**

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| 名稱     | ExternalTableName | 查詢 | ForcedLatency | IntervalBetweenRuns | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "[' DB ']。[' S '] "<br>] | {<br>  "SizeLimit"：104857600<br>} |

## <a name="show-continuous-export"></a>顯示連續匯出

**語法：**

`.show``continuous-export` *ContinuousExportName*

傳回*ContinuousExportName*的連續匯出屬性。 

**屬性**

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱。 |


`.show` `continuous-exports`

傳回資料庫中的所有連續匯出。 

**輸出**

| 輸出參數    | 類型     | 描述                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | 明確限定範圍（事實）資料表的清單（JSON 序列化）               |
| ExportProperties    | String   | 匯出屬性（JSON 序列化）                                     |
| ExportedTo          | Datetime | 已成功匯出的最後一個日期時間（內嵌時間）       |
| ExternalTableName   | String   | 外部資料表的名稱                                              |
| ForcedLatency       | TimeSpan | 強制延遲（如果未提供，則為 null）                                   |
| IntervalBetweenRuns | TimeSpan | 執行之間的間隔                                                   |
| IsDisabled          | Boolean  | 如果連續匯出已停用，則為 True                               |
| IsRunning           | Boolean  | 如果連續匯出目前正在執行，則為 True                      |
| LastRunResult       | String   | 上次連續匯出執行的結果（`Completed`或） `Failed` |
| LastRunTime         | Datetime | 上次執行連續匯出的時間（開始時間）           |
| 名稱                | String   | 連續匯出的名稱                                           |
| 查詢               | String   | 匯出查詢                                                            |
| StartCursor         | String   | 第一次執行此連續匯出的起點         |

## <a name="show-continuous-export-exported-artifacts"></a>顯示連續匯出匯出成品

**語法：**

`.show``continuous-export` *ContinuousExportName*`exported-artifacts`

傳回所有執行中由連續匯出匯出的所有成品。 建議您依照命令中的 Timestamp 資料行來篩選結果，只查看感興趣的記錄。 匯出成品的歷程記錄會保留14天。 

**屬性**

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱。 |

**輸出**

| 輸出參數  | 類型     | 描述                            |
|-------------------|----------|----------------------------------------|
| 時間戳記         | Datetime | 連續匯出執行的時間戳記 |
| ExternalTableName | String   | 外部資料表的名稱             |
| Path              | String   | 輸出路徑                            |
| NumRecords        | long     | 匯出至路徑的記錄數目     |

**範例：** 

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| 時間戳記                   | ExternalTableName | Path             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07：31：30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1024              |

## <a name="show-continuous-export-failures"></a>顯示連續匯出失敗

**語法：**

`.show``continuous-export` *ContinuousExportName*`failures`

傳回連續匯出過程中記錄的所有失敗。 依據命令中的 Timestamp 資料行來篩選結果，只查看感興趣的時間範圍。 

**屬性**

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱  |

**輸出**

| 輸出參數 | 類型      | 描述                                         |
|------------------|-----------|-----------------------------------------------------|
| 時間戳記        | Datetime  | 失敗的時間戳記。                           |
| OperationId      | String    | 失敗的作業識別碼。                    |
| 名稱             | String    | 連續匯出名稱。                             |
| LastSuccessRun   | 時間戳記 | 最後一次成功執行連續匯出。   |
| FailureKind      | String    | 失敗/PartialFailure。 PartialFailure 表示某些成品在失敗發生之前已成功匯出。 |
| 詳細資料          | String    | 失敗錯誤詳細資料。                              |

**範例：** 

```kusto
.show continuous-export MyExport failures 
```

| 時間戳記                   | OperationId                          | 名稱     | LastSuccessRun              | FailureKind | 詳細資料    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11：07：41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11：06：35.6308140 | 失敗     | 詳細資料 .。。 |

## <a name="drop-continuous-export"></a>捨棄連續匯出

**語法：**

`.drop``continuous-export` *ContinuousExportName*

**屬性**

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱 |

**輸出**

資料庫中剩餘的連續匯出（刪除後）。 輸出架構，如同 [[顯示連續匯出] 命令](#show-continuous-export)。

## <a name="disable-or-enable-continuous-export"></a>停用或啟用連續匯出

**語法：**

`.enable``continuous-export` *ContinuousExportName* 

`.disable``continuous-export` *ContinuousExportName* 

您可以停用或啟用連續匯出作業。 停用的連續匯出將不會執行，但其目前狀態會保存下來，而且可以在啟用連續匯出時繼續進行。 啟用已長時間停用的連續匯出時，會在停用時從上次停止的位置繼續匯出。 如果沒有足夠的叢集容量來處理所有進程，這可能會導致長時間執行的匯出，並封鎖其他匯出的執行。 連續匯出是依上次執行時間以遞增循序執行（最舊的匯出會先執行，直到趕上完成為止）。 

**屬性**

| 屬性             | 類型   | 描述                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | 連續匯出的名稱 |

**輸出**

改變連續匯出的 [[顯示連續匯出] 命令](#show-continuous-export)的結果。 




## <a name="exporting-historical-data"></a>匯出歷程記錄資料

「連續匯出」只會從其建立點開始匯出資料。 在該時間之前的記錄內嵌，應該使用（非連續）[匯出命令](export-data-to-an-external-table.md)個別匯出。 若要避免連續匯出匯出的資料發生重複，請使用 [[顯示連續匯出] 命令](#show-continuous-export)所傳回的 StartCursor，並在 cursor_before_or_at 資料指標值的情況下，只匯出記錄。 請參閱下方的範例。 歷程記錄資料可能太大，而無法在單一匯出命令中匯出。 因此，將查詢分割成幾個較小的批次。 

```kusto
.show continuous-export MyExport | project StartCursor
```

| StartCursor        |
|--------------------|
| 636751928823156645 |

後面接著： 

```kusto
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```
