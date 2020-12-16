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
ms.openlocfilehash: 166d5f4f4d81957c49fb3fdedd3b2654985648ab
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97514061"
---
# <a name="update-policy-overview"></a>更新原則總覽

[更新原則](update-policy.md)會根據在插入來源資料表的資料上執行的轉換查詢，指示 Kusto 自動將資料附加到目標資料表中的每個新資料。

:::image type="content" source="images/updatepolicy/update-policy-overview.png" alt-text="Azure 資料總管中的更新原則總覽":::

例如，原則可讓您建立一個資料表，做為另一個資料表的篩選視圖。 新的資料表可以有不同的架構、保留原則等等。 

更新原則受限於一般內嵌的相同限制和最佳作法。 原則會以叢集的大小相應放大，並在大型 bulks 中進行內嵌時更有效率。

> [!NOTE]
> 定義更新原則的來源資料表和資料表必須位於相同的資料庫中。
> 更新原則函數架構和目標資料表架構必須符合其資料行名稱、類型和順序。

## <a name="update-policys-query"></a>更新原則的查詢

「更新原則」查詢會以特殊模式執行，在此模式中，系統會自動將範圍設定為僅涵蓋新內嵌的記錄，而且您無法在此查詢中查詢來源資料表的已內嵌資料。 不過，在交易式更新原則的「界限」中內嵌的資料，在單一交易中會變成可供查詢使用。 因為更新原則是在目的地資料表上定義，所以將資料擷取成一個來源資料表可能會導致在該資料上執行多個查詢。 多個更新原則的執行順序未定義。 

### <a name="query-limitations"></a>查詢限制 

* 此查詢可以叫用預存函式，但不能包含跨資料庫或跨叢集的查詢。 
* 在更新原則中執行的查詢沒有啟用 [RestrictedViewAccess 原則](restrictedviewaccesspolicy.md) 或已啟用 [資料列層級安全性原則](rowlevelsecuritypolicy.md) 的資料表的讀取權限。
* 在原則的 `Source` `Query` 一部分或元件參考的函式中參考資料表時 `Query` ：
   * 請勿使用資料表的限定名稱。 請改用 `TableName`。 
   * 請勿使用 `database("DatabaseName").TableName` 或 `cluster("ClusterName").database("DatabaseName").TableName` 。
* 如需串流內嵌的更新原則限制，請參閱 [串流內嵌限制](../../ingest-data-streaming.md#limitations)。 

> [!WARNING]
> 在更新原則中定義不正確的查詢可以防止將任何資料內嵌到來源資料表。

## <a name="the-update-policy-object"></a>更新原則物件

資料表可以有零個、一個或多個相關聯的更新原則物件。
每個這類物件都會以 JSON 屬性包表示，並定義下列屬性。

|屬性 |類型 |描述  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |指出是否已啟用更新原則 (true) 或停用 (false)                                                                                                                                |
|來源                        |`string`|觸發要叫用更新原則的資料表名稱                                                                                                                                 |
|查詢                         |`string`|用來產生更新資料的 Kusto CSL 查詢                                                                                                                           |
|IsTransactional               |`bool`  |指出更新原則是否為交易式或未 (預設為 false) 。 無法執行交易式更新原則會導致來源資料表未以新資料更新   |
|PropagateIngestionProperties  |`bool`  |指出內嵌至來源資料表期間指定的內嵌屬性 (範圍標記和建立時間) ，也應套用至衍生資料表中的專案。                 |

> [!NOTE]
> 允許串聯更新 (`TableA` →→ `TableB` `TableC` → ) 。
>
> 但是，如果以迴圈方式定義多個資料表的更新原則，則會剪下更新鏈。 在執行時間偵測到此問題。 每個資料表的資料只會內嵌一次。

## <a name="update-policy-commands"></a>更新原則命令

控制更新原則的命令包括：

* [`.show table *TableName* policy update`](update-policy.md#show-update-policy) 顯示資料表目前的更新原則。
* [`.alter table *TableName* policy update`](update-policy.md#alter-update-policy) 設定資料表的目前更新原則。
* [`.alter-merge table *TableName* policy update`](update-policy.md#alter-merge-table-tablename-policy-update) 附加至資料表目前的更新原則。
* [`.delete table *TableName* policy update`](update-policy.md#delete-table-tablename-policy-update) 附加至資料表目前的更新原則。

## <a name="update-policy-is-initiated-following-ingestion"></a>更新原則會在內嵌之後起始

當資料已內嵌或移至 (範圍會在使用下列任何一個命令) 定義的來源資料表中建立時，更新原則便會生效：

* [。內嵌 (提取) ](../management/data-ingestion/ingest-from-storage.md)
* [。內嵌 (內嵌) ](../management/data-ingestion/ingest-inline.md)
* [。 set |. append |. set-或-append |. set-或-replace](../management/data-ingestion/ingest-from-query.md)
  * 當將更新原則作為命令的一部分來叫用時  `.set-or-replace` ，預設行為是衍生資料表中 (s) 的資料會以與來源資料表相同的方式來取代。
* [.move extents](./move-extents.md)
* [.replace extents](./replace-extents.md)
  * 此 `PropagateIngestionProperties` 命令只會在內嵌操作中生效。 當更新原則觸發為或命令的一部分時 `.move extents` `.replace extents` ，此選項不會有任何作用。

## <a name="regular-ingestion-using-update-policy"></a>使用更新原則的一般內嵌

當符合下列條件時，更新原則的行為就像一般內嵌一樣：

* 來源資料表是一種高比率的追蹤資料表，其中包含格式化為自由文字資料行的有趣資料。 
* 定義更新原則的目標資料表只接受特定的追蹤行。
* 資料表具有結構完善的架構，此架構會轉換 [剖析運算子](../query/parseoperator.md)所建立的原始自由文字資料。

## <a name="zero-retention-on-source-table"></a>來源資料表保留零

有時候，資料只會內嵌到來源資料表做為目標資料表的逐步執行，而且您不想要保留來源資料表中的原始資料。 在來源資料表的 [保留原則](retentionpolicy.md)中設定0的虛刪除期，並將更新原則設定為交易式。 在此情況下： 

* 來源資料無法從來源資料表進行查詢。 
* 在內嵌作業中，來源資料不會保存至永久性儲存體。 
* 操作效能將會改善。 
* 背景清理作業的後續內嵌資源將會降低。 這些作業會在來源資料表的 [範圍](../management/extents-overview.md) 中完成。

## <a name="performance-impact"></a>效能影響

更新原則可能會影響 Kusto 叢集的效能。 更新原則會影響任何內嵌到來源資料表。 多個資料範圍的內嵌會乘以目標資料表的數目。 因此， `Query` 更新原則的一部分必須經過優化，才能順利運作。 您可以針對內嵌作業測試更新原則的額外效能影響。 在建立或改變在其元件中使用的原則或函式之前，請先在特定和已存在的範圍上叫用原則 `Query` 。

### <a name="evaluate-resource-usage"></a>評估資源使用量

使用 [`.show queries`](../management/queries.md) ，在下列案例中評估資源使用量 (CPU、記憶體等等) ：
*  (`Source` 更新原則) 的屬性之來源資料表名稱 `MySourceTable` 。
* `Query`更新原則的屬性會呼叫名為的函式 `MyFunction()` 。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```

## <a name="failures"></a>失敗

依預設，執行更新原則失敗並不會影響將資料內嵌到來源資料表。 但是，如果更新原則定義為 `IsTransactional` ： true，執行原則的失敗會強制將資料內嵌到來源資料表中。 在某些情況下，將資料內嵌至來源資料表會成功，但是在內嵌至目標資料表期間，更新原則會失敗。

更新原則時所發生的失敗，可以使用[ `.show ingestion failures` 命令](../management/ingestionfailures.md)抓取。
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

### <a name="treatment-of-failures"></a>失敗的處理

#### <a name="non-transactional-policy"></a>非交易式原則 

Kusto 會忽略失敗。 任何重試是資料內嵌進程擁有者的責任。  

#### <a name="transactional-policy"></a>交易式原則

觸發更新的原始內嵌作業也會失敗。 來源資料表和資料庫不會以新資料修改。
如果內嵌方法是 `pull` (Kusto 的資料管理服務會包含在內嵌程式) 中，則整個內嵌作業會自動重試，並根據下列邏輯由 Kusto 的資料管理服務進行協調：
* 重試會完成，直到 `DataImporterMaximumRetryPeriod` (預設值 = 2 天) 和 `DataImporterMaximumRetryAttempts` (預設值 = 10) 為止。
* 您可以在資料管理服務的設定中更改上述兩個設定。
* 輪詢期間從2分鐘開始，並以指數方式成長 (2-> 4-> 8-> 16 .。。分鐘) 

在其他任何情況下，重試是資料擁有者的責任。