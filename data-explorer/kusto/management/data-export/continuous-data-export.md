---
title: 連續資料匯出-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的連續資料匯出。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: be16f33c649640ef92ed971665d4c7610c5501bf
ms.sourcegitcommit: c815c6ccf33864e21e1d3daff26a4f077dff88f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012204"
---
# <a name="continuous-data-export-overview"></a>連續資料匯出概觀

本文說明如何使用定期執行的查詢，將資料從 Kusto 連續匯出至 [外部資料表](../external-table-commands.md) 。 結果會儲存在外部資料表中，以定義目的地，例如 Azure Blob 儲存體，以及匯出資料的架構。 此程式可確保所有記錄都會「剛好一次」匯出，但有一些 [例外](#exactly-once-export)狀況。 

若要啟用連續資料匯出，請 [建立外部資料表](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table) ，然後建立指向外部資料表 [的連續匯出定義](create-alter-continuous.md) 。 

> [!NOTE]
> 所有連續匯出命令都需要 [資料庫系統管理員許可權](../access-control/role-based-authorization.md)。

## <a name="continuous-export-guidelines"></a>連續匯出指導方針

* **輸出架構**：
  * 匯出查詢的輸出架構必須符合您匯出之外部資料表的架構。 
* **頻率**：
  * 「連續匯出」會根據在屬性中所設定的時間週期執行 `intervalBetweenRuns` 。 此間隔的建議值至少為幾分鐘，視您願意接受的延遲而定。 如果內嵌速率很高，則時間間隔可以小到一分鐘。
* **散發**：
  * 連續匯出中的預設分佈是 `per_node` ，所有節點都會同時匯出。 
  * 您可以在連續匯出 create 命令的屬性中覆寫此設定。 使用 `per_shard` 散發來增加並行。
    > [!NOTE]
    > 此分佈會增加儲存體帳戶 () 的負載，並有機會達到節流限制。 
  * 使用 `single` (或 `distributed` = `false`) 完全停用散發。 這項設定可能會大幅降低連續匯出程式，並影響每個連續匯出反復專案中建立的檔案數目。 
* 檔案 **數目**：
  * 每個連續匯出反復專案中匯出的檔案數目，取決於外部資料表的資料分割方式。 如需詳細資訊，請參閱 [匯出至外部資料表命令](export-data-to-an-external-table.md#number-of-files)。 每個連續匯出反復專案一律會寫入至新的檔案，而且永遠不會附加至現有的檔案。 因此，匯出的檔案數目也取決於連續匯出的執行頻率。 Frequency 參數為 `intervalBetweenRuns` 。
* **外部資料表儲存體帳戶**：
  * 為了達到最佳效能，Azure 資料總管叢集和儲存體帳戶 (s) 應該在相同的 Azure 區域中共存。
  * 如果匯出的資料量很大，建議您設定外部資料表的多個儲存體帳戶，以避免進行儲存體節流。 如需詳細資訊，請參閱 [匯出命令期間的儲存體失敗](export-data-to-storage.md#failures-during-export-commands) 。

## <a name="exactly-once-export"></a>剛好一次匯出

為了保證「剛好一次」匯出，連續匯出會使用 [資料庫資料指標](../databasecursor.md)。 必須在查詢中參考的所有資料表上啟用[IngestionTime 原則](../ingestiontime-policy.md)，此查詢應該在匯出中「剛好一次」處理。 預設會在所有新建立的資料表上啟用此原則。

「剛好一次」匯出的保證只適用于「 [顯示匯出](show-continuous-artifacts.md)成品」命令中所報告的檔案。 連續匯出不保證每一筆記錄只會寫入至外部資料表一次。 如果在匯出開始之後發生失敗，而且部分成品已經寫入外部資料表，則外部資料表可能會包含重複專案。 如果寫入作業在完成前中止，外部資料表可能會包含損毀的檔案。 在這種情況下，不會從外部資料表中刪除成品，但不會在 [ [顯示匯出](show-continuous-artifacts.md)的成品] 命令中報告這些構件。 使用匯出的檔案可 `show exported artifacts command` 保證不會重複問題，也不會損毀。

## <a name="export-from-fact-and-dimension-tables"></a>從事實和維度資料表匯出

根據預設，匯出查詢中參考的所有資料表都會假設為 [事實資料表](../../concepts/fact-and-dimension-tables.md)。 因此，它們的範圍是資料庫資料指標。 語法會明確宣告哪些資料表的範圍 (事實) ，而不是 (維度) 的範圍。 如需 `over` 詳細資料，請參閱 [create 命令](create-alter-continuous.md) 中的參數。

匯出查詢只會包含自上一次匯出執行以來加入的記錄。 匯出查詢可能包含 [維度資料表](../../concepts/fact-and-dimension-tables.md) ，維度資料表中的所有記錄都會包含在所有匯出查詢中。 在連續匯出的事實和維度資料表之間使用聯結時，請記住，事實資料表中的記錄只會處理一次。 如果某些索引鍵遺漏維度資料表中的記錄時執行匯出，則會遺漏個別索引鍵的記錄，或針對匯出檔案中的維度資料行包含 null 值。 傳回遺漏或 null 記錄取決於查詢是使用內部或外部聯結。 `forcedLatency`連續匯出定義中的屬性在這種情況下很有用，在這種情況下，事實和維度資料表會在相同時間內內嵌以進行相符的記錄。

> [!NOTE]
> 不支援連續匯出維度資料表。 匯出查詢必須包含至少一個事實資料表。

## <a name="exporting-historical-data"></a>匯出歷程記錄資料

「連續匯出」只會開始從其建立點匯出資料。 記錄內嵌之前，應該使用非連續 [匯出命令](export-data-to-an-external-table.md)個別匯出該時間。 

歷程記錄資料可能太大，而無法在單一匯出命令中匯出。 如有需要，請將查詢分割成數個較小的批次。 

若要避免連續匯出匯出的資料重複，請使用「 `StartCursor` [顯示連續匯出」命令](show-continuous-export.md) 所傳回的，而「匯出」只會記錄資料 `where cursor_before_or_at` 指標值。 例如：

```kusto
.show continuous-export MyExport | project StartCursor
```

| StartCursor        |
|--------------------|
| 636751928823156645 |

其次： 

```kusto
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```

## <a name="resource-consumption"></a>資源耗用量

* 連續匯出對叢集的影響取決於連續匯出正在執行的查詢。 大部分的資源（例如 CPU 和記憶體）都是由查詢執行所取用。 
* 可同時執行的匯出作業數目受限於叢集的資料匯出容量。 如需詳細資訊，請參閱 [節流](../../management/capacitypolicy.md#throttling)。 如果叢集沒有足夠的容量可處理所有連續匯出，有些會開始延遲後。
* [顯示命令和查詢命令](../commands-and-queries.md)可以用來預估資源耗用量。 
  * 篩選 `| where ClientActivityId startswith "RunContinuousExports"` 以查看與連續匯出相關聯的命令和查詢。

## <a name="limitations"></a>限制

* 連續匯出不適用於使用串流內嵌內嵌的資料。 
* 無法在已啟用 [資料列層級安全性原則](../../management/rowlevelsecuritypolicy.md) 的資料表上設定連續匯出。
* 外部資料表的 `impersonate` [連接字串](../../api/connection-strings/storage.md)中不支援連續匯出。
* 連續匯出不支援跨資料庫和跨叢集呼叫。
* 連續匯出並非設計用來在 Azure 資料總管中持續串流資料。 連續匯出會以分散式模式執行，而所有節點會同時匯出。 如果每個執行所查詢的資料範圍很小，則連續匯出的輸出會是許多小型構件。 構件的數目取決於叢集中的節點數目。
* 如果連續匯出使用的成品是為了觸發事件方格通知，請參閱 [Event grid 檔中的已知問題一節](../../../ingest-data-event-grid-overview.md#known-event-grid-issues)。
