---
title: 建立具體化視圖-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中建立具體化視圖。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 807ca8a9bfd8d3f356fd849b6adc2102201513cf
ms.sourcegitcommit: 21dee76964bf284ad7c2505a7b0b6896bca182cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91057114"
---
# <a name="create-materialized-view"></a>。建立具體化-view

[具體化 view](materialized-view-overview.md)是對來源資料表的匯總查詢，代表單一摘要語句。
如需建立具體化視圖的一般資訊和指導方針，請參閱 [建立具體化視圖](materialized-view-overview.md#create-a-materialized-view)。

需要 [資料庫管理員](../access-control/role-based-authorization.md) 許可權。

具體化視圖一律以單一為基礎 `fact table` ，而且也可以參考一或多個 [`dimension tables`](../../concepts/fact-and-dimension-tables.md) 。 如需在具體化視圖中聯結維度資料表的限制，請參閱 [屬性](#properties)。 如需一般限制，請參閱 [建立具體化視圖的限制](materialized-view-overview.md#limitations-on-creating-materialized-views)。 

* 使用 [ [顯示作業](../operations.md#show-operations) ] 命令追蹤建立程式。
* 使用 [. cancel operation](#cancel-materialized-view-creation) 命令取消建立進程。

## <a name="syntax"></a>語法

`.create` [`async`] `materialized-view` <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <br>
*ViewName* `on table`*SourceTableName* <br>
`{`<br>&nbsp;&nbsp;&nbsp;&nbsp;*查詢*<br>`}`

## <a name="arguments"></a>引數

|引數|類型|描述
|----------------|-------|---|
|ViewName|String|具體化視圖名稱。 視圖名稱無法與相同資料庫中的資料表或函數名稱衝突，而且必須遵守 [識別碼命名規則](../../query/schema-entities/entity-names.md#identifier-naming-rules)。 |
|SourceTableName|String|定義此視圖的來源資料表名稱。|
|查詢|String|具體化 view 查詢。 如需詳細資訊，請參閱 [查詢](#query-argument)。|

### <a name="query-argument"></a>查詢引數

具體化 view 引數中使用的查詢受限於下列規則：

* 查詢引數應該參考成為具體化視圖來源的單一事實資料表、包含單一摘要運算子，以及由一或多個依運算式匯總的一或多個彙總函式。 摘要運算子必須一律是查詢中的最後一個運算子。

* 查詢不應包含相依于 `now()` 或的任何運算子 `ingestion_time()` 。 例如，查詢不應該有 `where Timestamp > ago(5d)` 。 具有匯總的具體化視圖 `arg_max` / `arg_min` / `any` 無法包含任何其他支援的彙總函式。 使用具體化視圖上的保留原則，限制 view 所涵蓋的時間長度。

* View `arg_max` / `arg_min` / `any` 可以是 (這些函式可同時在相同的 view) 或任何其他支援的函式中使用，但不能同時在相同的具體化視圖中使用。 
    例如， `SourceTable | summarize arg_max(Timestamp, *), count() by Id` 不支援。 

* 具體化視圖定義中不支援複合匯總。 例如，您可以將 `SourceTable | summarize Result=sum(Column1)/sum(Column2) by Id` 具體化 view 定義為：，而不是下列視圖： `SourceTable | summarize a=sum(Column1), b=sum(Column2) by Id` 。 在 view 查詢階段期間，執行- `ViewName | project Id, Result=a/b` 。 您可以將視圖的必要輸出（包括匯出資料行 (`a/b`) ）封裝在 [預存](../../query/functions/user-defined-functions.md)函式中。 存取預存函式，而不是直接存取具體化 view。

* 不支援跨叢集/跨資料庫查詢。

* 不支援 [external_table ( # B1 ](../../query/externaltablefunction.md) 和 [externaldata](../../query/externaldata-operator.md) 的參考。

## <a name="properties"></a>屬性

子句支援下列各項 `with(propertyName=propertyValue)` 。 所有屬性都是選擇性的。

|屬性|類型|描述 | 注意 |
|----------------|-------|---|---|
|回填|bool|是否要根據目前在 *SourceTable* () 中的所有記錄來建立視圖 `true` ，或從 [從現在開始] (`false`) 建立。 預設為 `false`。| 此命令必須是 `async` ，而且在建立完成之前，無法使用此視圖來進行查詢。 依回填的資料量而定，使用回填建立可能需要很長的時間。 它刻意「緩慢」，以確保它不會耗用太多叢集資源。 請參閱 [回填說明](materialized-view-overview.md#create-a-materialized-view) |
|effectiveDateTime|Datetime| 如果與一起指定 `backfill=true` ，則只會建立回填，並在日期時間之後內嵌記錄。 回填也必須設定為 true。 需要日期時間常值，例如 `effectiveDateTime=datetime(2019-05-01)`|
|dimensionTables|Array|View 中的維度資料表清單（以逗號分隔）。|  您必須在 view 屬性中明確地呼叫維度資料表。 使用維度資料表的聯結/查閱應使用 [查詢最佳做法](../../query/best-practices.md)。 請參閱 [加入維度資料表](#join-with-dimension-table)。
|autoUpdateSchema|bool|是否要在來源資料表變更時自動更新視圖。 預設為 `false`。| 只有 `autoUpdateSchema` `arg_max(Timestamp, *)`  /  `arg_min(Timestamp, *)`  /  `any(*)` 在) 資料行引數時，此選項才適用于型別 (的視圖 `*` 。 如果此選項設定為 true，則來源資料表的變更將會自動反映在具體化視圖中。 使用這個選項時，不支援所有來源資料表的變更。 如需詳細資訊，請參閱 [。 alter 具體化-view](materialized-view-alter.md)。 |
|folder|字串|具體化視圖的資料夾。|
|docString|字串|記錄具體化視圖的字串|

> [!WARNING]
> `autoUpdateSchema`當來源資料表中的資料行卸載時，使用可能會導致無法復原的資料遺失。 如果此 view 未設定為，則會停用 `autoUpdateSchema` ，而且會對來源資料表進行變更，以將架構變更變更為具體化視圖。 如果問題已修正，請使用 [ [啟用具體化 view](materialized-view-enable-disable.md) ] 命令重新啟用具體化 view。 
>
>使用 `arg_max(Timestamp, *)` 並將資料行加入至來源資料表時，此程式很常見。 將 view 查詢定義為或使用選項，以避免失敗 `arg_max(Timestamp, Column1, Column2, ...)` `autoUpdateSchema` 。 

### <a name="join-with-dimension-table"></a>與維度資料表聯結

視圖的來源資料表中的記錄 (事實資料表) 只會具體化一次。 事實資料表與維度資料表之間的不同內嵌延遲可能會影響視圖結果。

**範例**：視圖定義包含具有維度資料表的內部聯結。 在具體化時，維度記錄未完全內嵌，但已內嵌到事實資料表。 此記錄將會從視圖中卸載，且永遠不會重新處理。 

若要解決此情況，請假設聯結是外部聯結。 將會處理事實資料表中的記錄，並將其加入至維度資料表資料行的 null 值。 已新增 () 值為 null 值的記錄將不會再次處理。 它們在維度資料表的資料行中的值會維持 null。


## <a name="examples"></a>範例

1. 建立空的視圖，只會從現在開始具體化記錄內嵌： 

    <!-- csl -->
    ```
    .create materialized-view ArgMax on table T
    {
        T | summarize arg_max(Timestamp, *) by User
    }
    ```
    
1. 使用下列方法建立具有回填選項的具體化 view `async` ：

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, docString="Customer telemetry") CustomerUsage on table T
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day 
    } 
    ```
    
1. 使用回填和來建立具體化 view `effectiveDateTime` 。 此視圖是根據日期時間的記錄建立的：

    <!-- csl -->
    ```
    .create async materialized-view with (backfill=true, effectiveDateTime=datetime(2019-01-01)) CustomerUsage on table T 
    {
        T 
        | extend Day = bin(Timestamp, 1d)
        | summarize count(), dcount(User), max(Duration) by Customer, Day
    } 
    ```
    
1. 定義可以在語句之前包含其他運算子 `summarize` ，只要 `summarize` 是最後一個運算子即可：

    <!-- csl -->
    ```
    .create materialized-view CustomerUsage on table T 
    {
        T 
        | where Customer in ("Customer1", "Customer2", "CustomerN")
        | parse Url with "https://contoso.com/" Api "/" *
        | extend Month = startofmonth(Timestamp)
        | summarize count(), dcount(User), max(Duration) by Customer, Api, Month
    }
    ```
    
1. 與維度資料表聯結的具體化視圖：

    <!-- csl -->
    ```
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
    {
        T
        | lookup DimUsers on User  
        | summarize arg_max(Timestamp, *) by User 
    }
    
    .create materialized-view EnrichedArgMax on table T with (dimensionTable = ['DimUsers'])
    {
        DimUsers | project User, Age, Address
        | join kind=rightouter hint.strategy=broadcast T on User
        | summarize arg_max(Timestamp, *) by User 
    }
    ```
    

## <a name="supported-aggregation-functions"></a>支援的彙總函式

以下是支援的彙總函式：

* [`count`](../../query/count-aggfunction.md)
* [`countif`](../../query/countif-aggfunction.md)
* [`dcount`](../../query/dcount-aggfunction.md)
* [`dcountif`](../../query/dcountif-aggfunction.md)
* [`min`](../../query/min-aggfunction.md)
* [`max`](../../query/max-aggfunction.md)
* [`avg`](../../query/avg-aggfunction.md)
* [`avgif`](../../query/avgif-aggfunction.md)
* [`sum`](../../query/sum-aggfunction.md)
* [`arg_max`](../../query/arg-max-aggfunction.md)
* [`arg_min`](../../query/arg-min-aggfunction.md)
* [`any`](../../query/any-aggfunction.md)
* [`hll`](../../query/hll-aggfunction.md)
* [`make_set`](../../query/makeset-aggfunction.md)
* [`make_list`](../../query/makelist-aggfunction.md)
* [`percentile`, `percentiles`](../../query/percentiles-aggfunction.md)

## <a name="performance-tips"></a>效能秘訣

* 具體化視圖查詢篩選準則會在依 (匯總 by 子句) 的其中一個具體化 View 維度進行篩選時優化。 如果您知道您的查詢模式通常會依某些資料行篩選，而此資料行可以是具體化視圖中的維度，請將它包含在 view 中。 例如：若要公開的具體化視圖 `arg_max` 通常會 `ResourceId` 依篩選 `SubscriptionId` ，建議如下所示：

    **執行**：
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by SubscriptionId, ResouceId 
    }
    ``` 
    
    **請勿這樣做**：
    
    ```kusto
    .create materialized-view ArgMaxResourceId on table FactResources
    {
        FactResources | summarize arg_max(Timestamp, *) by ResouceId 
    }
    ```

* 請勿包含轉換、正規化，以及可移至 [更新原則](../updatepolicy.md) 做為具體化 view 定義一部分的其他繁重計算。 相反地，請在更新原則中執行所有的處理常式，並只在具體化視圖中執行匯總。 如果適用，請使用此程式來查閱維度資料表。

    **執行**：
    
    * 更新原則：
    
    ```kusto
    .alter-merge table Target policy update 
    @'[{"IsEnabled": true, 
        "Source": "SourceTable", 
        "Query": 
            "SourceTable 
            | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)", 
        "IsTransactional": false}]'  
    ```
        
    * 具體化視圖：
    
    ```kusto
    .create materialized-view Usage on table Events
    {
    &nbsp;     Target 
    &nbsp;     | summarize count() by ResourceId 
    }
    ```
    
    **請勿這樣做**：
    
    ```kusto
    .create materialized-view Usage on table SourceTable
    {
    &nbsp;     SourceTable 
    &nbsp;     | extend ResourceId = strcat('subscriptions/', toupper(SubscriptionId), '/', resourceId)
    &nbsp;     | summarize count() by ResourceId
    }
    ```

> [!NOTE]
> 如果您需要最佳查詢時間效能，但可能會犧牲一些資料的有效期限，請使用 [materialized_view ( # A1 函數](../../query/materialized-view-function.md)。

## <a name="cancel-materialized-view-creation"></a>取消具體化視圖建立

使用選項時，請取消具體化視圖建立的進程 `backfill` 。 當建立花費的時間太長，而您想要在執行期間中止它時，此動作會很有用。  

> [!WARNING]
> 執行此命令之後，就無法還原具體化 view。

無法立即中止建立程式。 Cancel 命令會通知具體化停止，並定期建立檢查是否已要求取消。 Cancel 命令會等待最多10分鐘的時間，直到具體化視圖建立程式取消並在取消作業成功時回報。 即使在10分鐘內取消作業失敗，且 cancel 命令回報失敗，具體化視圖也可能會在稍後的建立程式中自行中止。 [ [顯示作業](../operations.md#show-operations) ] 命令會指出是否已取消操作。 `cancel operation`只有具體化視圖建立取消支援命令，而不支援取消任何其他作業。

### <a name="syntax"></a>語法

`.cancel``operation` *operationId*

### <a name="properties"></a>屬性

|屬性|類型|描述
|----------------|-------|---|
|operationId|Guid|從 create 具體化 view 命令傳回的作業識別碼。|

### <a name="output"></a>輸出

|輸出參數 |類型 |描述
|---|---|---
|OperationId|Guid|Create 具體化 view 命令的作業識別碼。
|作業|String|作業種類。
|StartedOn|Datetime|建立作業的開始時間。
|CancellationState|字串|其中一個 `Cancelled successfully` (的建立已取消) ， `Cancellation failed` (等候取消超時) ， `Unknown` (視圖建立已不再執行，但這項作業) 尚未取消。
|ReasonPhrase|字串|取消失敗的原因。

### <a name="example"></a>範例

<!-- csl -->
```
.cancel operation c4b29441-4873-4e36-8310-c631c35c916e
```

|OperationId|作業|StartedOn|CancellationState|ReasonPhrase|
|---|---|---|---|---|
|c4b29441-4873-4e36-8310-c631c35c916e|MaterializedViewCreateOrAlter|2020-05-08 19：45：03.9184142|已成功取消||

如果取消未在10分鐘內完成， `CancellationState` 則會指出失敗。 之後可能會中止建立。
