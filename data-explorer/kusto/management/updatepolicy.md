---
title: Kusto 新增至來源之資料的更新原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的更新原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/04/2020
ms.openlocfilehash: 9b2d35c796cfd1f41dc2fd8e9385a4c446000b86
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88072441"
---
# <a name="update-policy-overview"></a>更新原則總覽

[更新原則](update-policy.md)會指示 Kusto 在每次新資料插入來源資料表時，自動將資料附加至目標資料表，這是根據插入來源資料表之資料上執行的轉換查詢。

:::image type="content" source="images/updatepolicy/update-policy-overview.png" alt-text="Azure 資料總管中的更新原則總覽":::

例如，原則可讓您建立一個資料表做為另一個資料表的篩選視圖。 新的資料表可以有不同的架構、保留原則等等。 

更新原則受限於一般內嵌的相同限制和最佳作法。 原則會以叢集的大小向外延展，如果擷取在大型 bulks 中執行，則會更有效率地運作。

> [!NOTE]
> 定義更新原則的來源資料表和資料表必須位於相同的資料庫中。
> 更新原則函數架構和目標資料表架構必須符合其資料行名稱、類型和順序。

## <a name="update-policys-query"></a>更新原則的查詢

「更新原則」查詢是在特殊模式下執行，其中會自動將其範圍限定為只涵蓋新內嵌的記錄，而且您無法在此查詢中查詢來源資料表的已內嵌資料。 不過，交易式更新原則「界限」中的資料內嵌，會在單一交易中提供查詢使用。 由於更新原則是在目的地資料表上定義的，因此將資料內嵌到一個來源資料表可能會導致在該資料上執行多個查詢。 多個更新原則的執行順序未定義。 

### <a name="query-limitations"></a>查詢限制 

* 查詢可以叫用預存函數，但不能包含跨資料庫或跨叢集查詢。 
* 當做更新原則之一部分執行的查詢沒有已啟用[RestrictedViewAccess 原則](restrictedviewaccesspolicy.md)，或已啟用[資料列層級安全性原則](rowlevelsecuritypolicy.md)之資料表的讀取存取權。
* 參考 `Source` `Query` 原則部分或元件所參考之函式中的資料表時 `Query` ：
   * 請勿使用資料表的限定名稱。 請改用 `TableName`。 
   * 請勿使用 `database("DatabaseName").TableName` 或 `cluster("ClusterName").database("DatabaseName").TableName` 。

> [!WARNING]
> 在更新原則中定義不正確的查詢，可能會導致無法將任何資料內嵌到來源資料表中。

## <a name="the-update-policy-object"></a>更新原則物件

資料表可以有零個、一個或多個與其相關聯的更新原則物件。
每個這類物件都會表示為 JSON 屬性包，並已定義下列屬性。

|屬性 |類型 |描述  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |指出是否已啟用更新原則 (true) 或停用 (false)                                                                                                                                |
|來源                        |`string`|觸發要叫用之更新原則的資料表名稱                                                                                                                                 |
|查詢                         |`string`|用來產生更新資料的 Kusto CSL 查詢                                                                                                                           |
|IsTransactional               |`bool`  |指出更新原則是否為交易式 (預設為 false) 。 無法執行交易式更新原則會導致來源資料表未以新資料更新   |
|PropagateIngestionProperties  |`bool`  |狀態：如果內嵌屬性 (範圍標籤和建立時間) 在內嵌至來源資料表期間指定），則也應該套用至衍生資料表中的那些內容。                 |

> [!NOTE]
> 允許串聯式更新 (`TableA` → `TableB` → `TableC` → ... ) 。
>
> 不過，如果以迴圈方式定義多個資料表的更新原則，則會剪下更新鏈。 此問題會在執行時間中偵測到。 資料只會針對受影響資料表鏈中的每個資料表內嵌一次。

## <a name="update-policy-commands"></a>更新原則命令

控制更新原則的命令包括：

* [。顯示資料表*TableName*原則更新](update-policy.md#show-update-policy)會顯示資料表目前的更新原則。
* [。 alter Table *TableName*原則更新](update-policy.md#alter-update-policy)會設定資料表的目前更新原則。
* [。 alter-merge 資料表*TableName*原則更新](update-policy.md#alter-merge-table-tablename-policy-update)會附加至資料表的目前更新原則。
* [。刪除資料表*TableName*的原則更新](update-policy.md#delete-table-tablename-policy-update)會附加至資料表的目前更新原則。

## <a name="update-policy-is-initiated-following-ingestion"></a>更新原則是在內嵌後起始

當您使用下列任何命令，將資料內嵌或移至) 定義的來源資料表中建立 (範圍時，更新原則就會生效：

* [。內嵌 (提取) ](../management/data-ingestion/ingest-from-storage.md)
* [ (內嵌) ](../management/data-ingestion/ingest-inline.md)
* [。 set |. append |. set-或-append |. set-或-replace](../management/data-ingestion/ingest-from-query.md)
  * 將更新原則當做命令的一部分叫用時 `.set-or-replace` ，預設行為是衍生資料表 (s) 中的資料會以與來源資料表相同的方式來取代。
* [.move extents](../management/extents-commands.md#move-extents)
* [.replace extents](../management/extents-commands.md#replace-extents)
  * 此 `PropagateIngestionProperties` 命令只會在內嵌作業中生效。 當更新原則當做或命令的一部分觸發時 `.move extents` `.replace extents` ，這個選項沒有任何作用。

## <a name="regular-ingestion-using-update-policy"></a>使用更新原則進行一般內嵌

當符合下列條件時，更新原則的行為就像一般內嵌：

* 來源資料表是一種高比率的追蹤資料表，其中有有趣的資料會格式化為任意文字資料行。 
* 定義更新原則的目標資料表只接受特定的追蹤行。
* 資料表具有結構良好的架構，它是由[parse 運算子](../query/parseoperator.md)所建立之原始自由文字資料的轉換。

## <a name="zero-retention-on-source-table"></a>來源資料表的保留期為零

有時資料只會內嵌到來源資料表做為目標資料表的逐步石，而且您不會想要將原始資料保留在來源資料表中。 在來源資料表的[保留原則](retentionpolicy.md)中設定0的虛刪除週期，並將更新原則設定為交易式。 在此情況下： 

* 來源資料無法從來源資料表進行查詢。 
* 在內嵌作業中，來源資料不會保存到永久性儲存體。 
* 操作效能將會改善。 
* 將降低背景清理作業的後續內嵌資源。 這些作業會在來源資料表的[範圍](../management/extents-overview.md)內完成。

## <a name="performance-impact"></a>效能影響

更新原則可能會影響 Kusto 叢集的效能。 更新原則會影響到來源資料表中的任何內嵌。 內嵌多個資料範圍會乘以目標資料表的數目。 因此， `Query` 更新原則的部分必須經過優化，才能順利運作。 您可以測試更新原則對內嵌作業的額外效能影響。 在建立或改變在其元件中使用的原則或函式之前，先在特定且已存在的範圍上叫用原則 `Query` 。

### <a name="evaluate-resource-usage"></a>評估資源使用量

使用 [[顯示查詢](../management/queries.md)]，在下列案例中評估資源使用量 (CPU、記憶體等等) ：
* 來源資料表名稱 (`Source` 更新原則的屬性) 為 `MySourceTable` 。
* `Query`更新原則的屬性會呼叫名為的函式 `MyFunction()` 。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```

## <a name="failures"></a>失敗

根據預設，無法執行更新原則並不會影響將資料內嵌至來源資料表。 不過，如果更新原則定義為 `IsTransactional` ： true，則無法執行原則會強制將資料內嵌至來源資料表，使其失敗。 在某些情況下，將資料內嵌到來源資料表的作業會成功，但是更新原則會在內嵌至目標資料表期間失敗。

在更新原則時所發生的失敗，可以使用來抓取[。 [顯示內嵌失敗] 命令](../management/ingestionfailures.md)。
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

### <a name="treatment-of-failures"></a>失敗的處理

#### <a name="non-transactional-policy"></a>非交易式原則 

Kusto 會忽略失敗。 任何重試都是資料內嵌進程擁有者的責任。  

#### <a name="transactional-policy"></a>交易式原則

觸發更新的原始內嵌作業也會失敗。 不會使用新的資料來修改來源資料表和資料庫。
如果內嵌方法是 `pull` (Kusto 的資料管理服務包含在內嵌程式) 中，則整個內嵌作業會自動重試，Kusto 的資料管理服務會根據下列邏輯進行協調：
* 重試會完成，直到 `DataImporterMaximumRetryPeriod` (預設值 = 2 天) 和 `DataImporterMaximumRetryAttempts` (預設值 = 10) 達到最早的時間。
* 這兩個設定都可以在資料管理服務的設定中改變。
* 輪詢期間會從2分鐘開始，並以指數方式成長 (2-> 4-> 8-> 16 .。。分鐘) 

在任何其他情況下，任何重試都是資料擁有者的責任。
