---
title: Kusto 新增至來源之資料的更新原則-Azure 資料總管
description: 本文說明 Azure 資料總管中的更新原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 072c908109fecb695a8961c546deb756caf830ab
ms.sourcegitcommit: 98eabf249b3f2cc7423dade0f386417fb8e36ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868694"
---
# <a name="update-policy"></a>更新原則

更新原則是在**目標資料表**上設定的，會指示 Kusto 在每次將新資料插入**來源資料表**時，自動附加資料給它。 更新原則的**查詢**會在插入來源資料表的資料上執行。 例如，這可讓您建立一個資料表做為另一個資料表的篩選視圖，可能會有不同的架構、保留原則等等。

根據預設，無法執行更新原則並不會影響將資料內嵌至來源資料表。 如果更新原則定義為**交易**式，則執行更新原則的失敗也會強制將資料內嵌到來源資料表中。 （當完成這項作業時必須小心，因為有些使用者錯誤（例如在更新原則中定義不正確的查詢）可能會防止**任何**資料內嵌到來源資料表中。）交易式更新原則的「界限」中的資料內嵌，可供單一交易中的查詢使用。

更新原則的查詢是在特殊模式中執行，它會將新內嵌的資料只「查看」到來源資料表。 在此查詢中，無法查詢來源資料表的已內嵌資料。 這麼做很快就會產生二次擷取。

因為更新原則是在目的地資料表上定義，所以將資料內嵌到一個來源資料表可能會導致在該資料上執行多個查詢。 更新原則的執行順序未定義。

例如，假設來源資料表是高比率的追蹤資料表，並將感興趣的資料格式化為任意文字資料行。 此外，假設目標資料表（定義更新原則）只接受特定的追蹤行，並使用結構良好的架構，這是使用 Kusto 的[parse 運算子](../query/parseoperator.md)轉換原始的自由文字資料。

更新原則的行為類似于一般內嵌，並且受限於相同的限制和最佳作法。 例如，它會以叢集的大小向外延展，而且如果在大型 bulks 中執行擷取，就能更有效率地運作。

## <a name="commands-that-trigger-the-update-policy"></a>觸發更新原則的命令

當您使用下列任何一個命令，將資料內嵌或移至資料表時，更新原則會生效（以建立範圍）：

* [. 內嵌（提取）](../management/data-ingestion/ingest-from-storage.md)
* [內嵌式（內嵌）](../management/data-ingestion/ingest-inline.md)
* [。 set |. append |. set-或-append |. set-或-replace](../management/data-ingestion/ingest-from-query.md)
* [。移動範圍](../management/extents-commands.md#move-extents)
* [。取代範圍](../management/extents-commands.md#replace-extents)

## <a name="the-update-policy-object"></a>更新原則物件

資料表可以有零個、一個或多個與其相關聯的更新原則物件。
每個這類物件都會以 JSON 屬性包表示，其中定義了下列屬性：

|屬性 |類型 |描述  |
|---------|---------|----------------|
|IsEnabled                     |`bool`  |是否啟用更新原則（true）或停用（false）的狀態                                                                                                                               |
|來源                        |`string`|觸發要叫用之更新原則的資料表名稱                                                                                                                                 |
|查詢                         |`string`|用來產生更新資料的 Kusto CSL 查詢                                                                                                                           |
|IsTransactional               |`bool`  |指出更新原則是否為交易式（預設為 false）。 如果無法執行交易式更新原則，則會導致來源資料表未使用新的資料更新。   |
|PropagateIngestionProperties  |`bool`  |狀態：在內嵌至來源資料表期間指定的內嵌屬性（範圍標記和建立時間）也應該套用至衍生資料表中的查詢。                 |

## <a name="notes"></a>備忘錄

* 查詢會自動限定範圍，僅涵蓋新內嵌的記錄。
* 查詢可以叫用預存函數。
* 允許串聯式更新（`TableA` → `TableB` → `TableC` → ...）
* 當將更新原則當做`.set-or-replace`命令的一部分叫用時，預設行為是衍生資料表中的資料也會被取代，因為它是在來源資料表中。

## <a name="limitations"></a>限制

* 定義更新原則的來源資料表和資料表**必須位於相同的資料庫中**。
* 查詢可能**不**會包含跨資料庫或跨叢集的查詢。
* 如果以迴圈方式定義多個資料表的更新原則，則會在執行時間偵測到這種情況，並剪下更新鏈（也就是說，資料只會內嵌一次到受影響的資料表鏈中的每個資料表）。
* 參考`Source`原則`Query`部分（或後者所參考的函式）中的資料表時，請確定您**未**使用資料表的限定名稱（也就是，使用`TableName` ，而**不** `database("DatabaseName").TableName`是或`cluster("ClusterName").database("DatabaseName").TableName`）。
* 當做更新原則之一部分執行的**查詢沒有已**啟用[RestrictedViewAccess 原則](restrictedviewaccesspolicy.md)之資料表的讀取存取權。
* 更新原則的查詢無法參考任何已啟用[資料列層級安全性原則](./rowlevelsecuritypolicy.md)的資料表。
* `PropagateIngestionProperties`只有在內嵌作業中才會生效。 當更新原則當做`.move extents`或`.replace extents`命令的一部分觸發時，這個選項**沒有任何**作用。

## <a name="retention-policy-on-the-source-table"></a>來源資料表上的保留原則

因此，若不保留來源資料表中的原始資料，您可以在來源資料表的[保留原則](retentionpolicy.md)中設定0的虛刪除期間，同時將更新原則設定為交易式。

這會導致：
* 來源資料無法從來源資料表中進行查詢。
* 在內嵌作業中，來源資料不會保存到永久性儲存體。
* 改善作業的效能。
* 針對在來源資料表中的[範圍](../management/extents-overview.md)進行的背景清理作業，減少使用後置內嵌的資源。

## <a name="failures"></a>失敗

在某些情況下，將資料內嵌到來源資料表的作業會成功，但是更新原則會在內嵌至目標資料表期間失敗。

更新原則時遇到的失敗，可以使用來抓取[。顯示內嵌失敗命令](../management/ingestionfailures.md)，如下所示：
 
```kusto
.show ingestion failures 
| where FailedOn > ago(1hr) and OriginatesFromUpdatePolicy == true
```

失敗的處理方式如下：

* **非交易式原則**： Kusto 會忽略失敗。 任何重試都是資料擁有者的責任。  
* **交易式原則**：觸發更新的原始內嵌作業也會失敗。 將不會使用新的資料來修改來源資料表和資料庫。
  * 如果內嵌方法是（Kusto `pull`的資料管理服務與內嵌程式相關），則整個內嵌作業會自動重試，並根據 Kusto 的資料管理服務，依據下列邏輯進行協調：
    * 重試會完成，直到達到`DataImporterMaximumRetryPeriod` （預設值 = 2 天）和`DataImporterMaximumRetryAttempts` （預設值 = 10）的最早時間為止。
    * 在資料管理服務的設定中，您可以藉由 KustoOps 來變更這兩個設定。
    * 輪詢期間會從2分鐘開始，並以指數方式成長（2-> 4-> 8-> 16 .。。細節
  * 在任何其他情況下，任何重試都是資料擁有者的責任。



## <a name="control-commands"></a>控制命令

* 使用[. 顯示資料表資料表原則更新](../management/update-policy.md#show-update-policy)，以顯示資料表的目前更新原則。
* 使用[. alter TABLE table policy update](../management/update-policy.md#alter-update-policy)來設定資料表的目前更新原則。
* 使用[. alter-merge 資料表資料表原則更新](../management/update-policy.md#alter-merge-table-table-policy-update)，附加至資料表的目前更新原則。
* 使用[. 刪除資料表資料表原則更新](../management/update-policy.md#delete-table-table-policy-update)，以附加至資料表的目前更新原則。

## <a name="testing-an-update-policys-performance-impact"></a>測試更新原則對效能的影響

定義更新原則可能會影響 Kusto 叢集的效能，因為它會影響到來源資料表的任何內嵌。 強烈建議將更新原則的`Query`部分優化，以順利執行。
您可以在建立或改變原則和（或）它在其`Query`元件中使用的函式之前，對內嵌作業測試更新原則的額外效能影響。

下列範例假設：

* 來源資料表名稱（更新原則`Source`的屬性）為`MySourceTable`。
* 更新`Query`原則的屬性會呼叫名為`MyFunction()`的函式。

使用[. 顯示查詢](../management/queries.md)，您可以評估下列查詢的資源使用量（CPU、記憶體等），以及（或）多次執行。

```kusto
.show table MySourceTable extents;
// The following line provides the extent ID for the not-yet-merged extent in the source table which has the most records
let extentId = $command_results | where MaxCreatedOn > ago(1hr) and MinCreatedOn == MaxCreatedOn | top 1 by RowCount desc | project ExtentId;
let MySourceTable = MySourceTable | where extent_id() == toscalar(extentId);
MyFunction()
```
