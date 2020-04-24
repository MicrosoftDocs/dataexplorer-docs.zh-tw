---
title: 更新策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的更新策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 812a5af932f69d898bb419631fa6ea3c6bc0795e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519392"
---
# <a name="update-policy"></a>更新原則

更新策略設置在**目標表**上,指示 Kusto 在源**表中**插入新數據時自動將數據追加到它。 更新策略的**查詢**在插入到源表中的數據上運行。 例如,這允許創建一個表作為另一個表的篩選檢視,可能使用不同的架構、保留策略等。

默認情況下,運行更新策略失敗不會影響將數據引入源表。 如果更新策略定義為**事務性**,則運行更新策略失敗會強制將數據引入源表也失敗。 (執行此操作時必須使用 Care,因為某些使用者錯誤(如在更新策略中定義不正確的查詢)可能會阻止**將任何**資料引入來源表中。在事務更新策略的"邊界"中引入的數據可用於單個事務中的查詢。

更新策略的查詢以特殊模式運行,在這種模式中,它只"看到"源表中新引入的數據。 無法作為此查詢的一部分查詢源表已引入的數據。 這樣做會迅速導致二次攝入。

由於更新策略是在目標表上定義的,因此將數據引入一個源表可能會導致在該數據上運行多個查詢。 更新策略的執行順序未定義。

例如,假設源表是一個高速率跟蹤表,其有趣的數據格式為自由文本列。 還假設目標表(在其中定義更新策略)只接受特定的跟蹤行,並且使用結構良好的架構,即通過使用 Kusto 的[解析運算符](../query/parseoperator.md)轉換原始自由文本數據。

更新策略的行為與常規引入類似,並受到相同的限制和最佳做法的約束。 例如,它隨群集的大小進行擴展,並且如果以大批量方式完成引入,則更高效地工作。

## <a name="commands-that-trigger-the-update-policy"></a>觸發更新原則的指令

當資料被引入或移動到(在)表中建立時,使用以下任何命令,更新策略將生效:

* [.ing0(拉)](../management/data-ingestion/ingest-from-storage.md)
* [.ing0(內聯)](../management/data-ingestion/ingest-inline.md)
* [.set = .附加 = .設定或追加 = .設定或取代](../management/data-ingestion/ingest-from-query.md)
* [.移動範圍](../management/extents-commands.md#move-extents)
* [.取代延伸盤區](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>更新原則物件

表可能具有與其關聯的零、一個或多個更新策略物件。
每個此類物件都表示為 JSON 屬性包,並定義了以下屬性:

|屬性 |類型 |描述  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |開啟更新政策 (true) 或關閉狀態(false)                                                                                                                               |
|來源                        |`string`|觸發要呼叫的更新策略的表的名稱                                                                                                                                 |
|查詢                         |`string`|產生更新資料的 Kusto CSL 查詢                                                                                                                           |
|是事務性的               |`bool`  |如果更新策略是事務性的,則表示(預設為 false)。 無法執行事務更新策略會導致源表也不會使用新數據進行更新。   |
|傳播屬性  |`bool`  |如果引入源表中期間指定的引入屬性(範圍標記和創建時間)也應應用於派生表中的屬性,則說明。                 |

> [!NOTE]
>
> * 為其定義更新策略的源表和表**必須位於同一資料庫中**。
> * 查詢**可能不包括**跨資料庫查詢或跨群集查詢。
> * 查詢可以調用存儲的函數。
> * 查詢會自動限定範圍,僅涵蓋新引入的記錄。
> * 允許級聯更新(表A`[update]`-- --`[update]`>`[update]`表 B -- > 表C -- > ...)
> * 如果以迴圈方式在多個表上定義更新策略,則在運行時檢測到此策略,並剪切更新鏈(這意味著,資料將僅引入一次到受影響表鏈中的每個表)。
> * 在策略部分`Query``Source`( 或後者引用的函數中)引用表時,請確保**不使用**表的限定名稱(`TableName`含義、使用**和不使用**`database("DatabaseName").TableName``cluster("ClusterName").database("DatabaseName").TableName`)。
> * 更新策略的查詢無法引用已啟用的[行級別安全原則](./rowlevelsecuritypolicy.md)的任何表。
> * 作為更新策略的一部分運行的查詢對啟用[了受限檢視存取策略](restrictedviewaccesspolicy.md)的表**沒有**讀取存取許可權。
> * `PropagateIngestionProperties`僅在攝入操作中生效。 當更新策略作為`.move extents``.replace extents`或 指令的一部份觸發時,這個選項**不起作用**。
> * 當作為命令的一`.set-or-replace`部分調用更新策略時,默認行為是,派生表中的數據也會被替換,就像在源表中一樣。

## <a name="retention-policy-on-the-source-table"></a>來源表上的保留原則

為了不保留源表中的原始資料,可以在源表的[保留策略](retentionpolicy.md)中設置軟刪除週期 0,同時將更新策略設置為事務性。

這將導致:
* 源數據無法從源表查詢。
* 源數據不會作為引入操作的一部分持久存儲而持久存儲。
* 改進操作性能。
* 減少使用後使用的資源,用於在源表中[的擴展盤區](../management/extents-overview.md)進行後台整理操作。

## <a name="failures"></a>失敗

在某些情況下,將數據引入源表成功,但更新策略在引入目標表期間失敗。

可以使用[.show 引入失敗命令](../management/ingestionfailures.md)檢索更新策略時遇到的故障,如下所示:
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

容錯處理如下:

* **非事務原則**:庫托忽略失敗。 任何重試都是數據所有者的責任。  
* **事務策略**:觸發更新的原始引入操作也將失敗。 源表和資料庫將不會使用新數據進行修改。
  * 如果引入方法是`pull`(Kusto 的數據管理服務涉及引入過程),則根據以下邏輯對整個引入操作進行自動重試,該操作由 Kusto 的數據管理服務協調:
    * 重試完成,直到達到最早介於`DataImporterMaximumRetryPeriod`( 預設`DataImporterMaximumRetryAttempts`= 2 天 ) 和 (預設值 = 10) 之間。
    * 上述兩種設置都可以在數據管理服務的配置中由 KustoOps 更改。
    * 回退期從2分鐘開始,呈指數級增長(2-> 4-> 8-> 16 ...分鐘)
  * 在任何其他情況下,任何重試都是數據所有者的責任。



## <a name="control-commands"></a>控制命令

* 使用[.show 表 TABLE 策略更新](../management/update-policy.md#show-update-policy)顯示表的當前更新策略。
* 使用[.alter 表表策略更新](../management/update-policy.md#alter-update-policy)設置表的當前更新策略。
* 使用[.alter-合併表表策略更新](../management/update-policy.md#alter-merge-table-table-policy-update)追加到表的當前更新策略。
* 使用[.delete 表 TABLE 策略更新](../management/update-policy.md#delete-table-table-policy-update)追加到表的當前更新策略。

## <a name="testing-an-update-policys-performance-impact"></a>測試更新政策的效能影響

定義更新策略可能會影響 Kusto 群集的性能,因為它會影響源表的任何引入。 強烈建議`Query`優化更新策略的部分以執行良好操作。
在創建或更改策略和/或它在其部分使用的函數之前,可以通過在特定和已經存在的擴展盤區上調用更新策略對引入操作的其他性能影響來測試`Query`更新策略的其他性能影響。

下列範例假設：

* 來源表名稱(更新策略`Source`的屬性)為`MySourceTable`。
* 更新`Query`策略的屬性調用名為`MyFunction()`的 函數。

使用[.show 查詢](../management/queries.md),可以評估以下查詢的資源使用方式(CPU、記憶體等)和/或多次執行。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```