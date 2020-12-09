---
title: " (預覽) 儲存的查詢結果-Azure 資料總管"
description: 本文說明如何在 Azure 資料總管中建立及使用預存查詢結果。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
ms.date: 12/3/2020
ms.openlocfilehash: 352f82bdb11847574807f81bc63236127900ae94
ms.sourcegitcommit: 202357f866801aafd92e3e29a84bb312e17aebc7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96933854"
---
# <a name="stored-query-results-preview"></a> (預覽) 儲存的查詢結果

儲存重量級查詢的結果，並快速加以抓取。
儲存的查詢結果在下列案例中很有用：
* 執行結果分頁。 預存查詢結果會根據查詢建立，而預覽會顯示在第一頁上。 每個後續頁面都會顯示預先計算結果的下一個部分，而不需要再次執行初始查詢。
* 在資料探索期間暫時儲存查詢結果。

> [!NOTE]
> 儲存的查詢結果會處於預覽階段，不應該用於生產案例。 

您可以從建立後的最多24小時存取儲存的查詢結果。 安全性原則的更新 (例如，資料庫存取、資料列層級安全性等 ) 不會傳播至儲存的查詢結果。 使用. 如果使用者權限撤銷，請使用 [drop stored_query_results](#drop-stored_query_results) 。 預存查詢結果只能由建立它的相同主體身分識別存取。 

預存查詢結果的行為就像資料表一樣，因為不會保留記錄的順序。 若要逐頁查看結果，建議查詢包含唯一的識別碼資料行。 如需詳細資訊，請參閱[範例](#examples)。 如果查詢傳回多個結果集，則只會儲存第一個結果集。 

使用儲存的查詢結果需要 `Database Viewer` 或更高的存取角色。

## <a name="store-the-results-of-a-query"></a>儲存查詢的結果

**語法**

`.set``stored_query_result` *StoredQueryResultName* [ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <|*查詢*

**引數**

* *StoredQueryResultName*：符合 [機構名稱](../query/schema-entities/entity-names.md) 規則的預存查詢結果名稱。
* *查詢*：可能會儲存其結果的 KQL 查詢。
* *PropertyName*： (所有屬性都是選擇性的) 
    
    | 屬性       | 類型       | 描述       |
    |----------------|------------|-------------------------------------------------------------------------------------|
    | `expiresAfter` | `timespan` | Timespan 常值，指出儲存的查詢結果將會 (最多24小時) 到期。 |
    | `previewCount` | `int`      | 要在預覽中傳回的資料列數目。 將這個屬性設定為 `0` (預設) 會讓命令傳回所有查詢結果資料列。 |
    | `distributed`  | `bool`     | 指出此命令會以平行方式儲存所有執行查詢的節點查詢結果。 預設值為 *true*。 `distributed`當查詢所產生的資料量很小，或許多叢集節點很大時，將旗標設為 *false* 會很有用，以防止建立許多小型資料分區。 |

## <a name="retrieve-a-stored-query-result"></a>取出儲存的查詢結果

若要取出儲存的查詢結果，請 `stored_query_result()` 在查詢中使用函數：

`stored_query_result``(`' StoredQueryResultName ' `)` `|`...

## <a name="examples"></a>範例

### <a name="simple-query"></a>簡單查詢

儲存簡單的查詢結果：

<!-- csl -->
```kusto
.set stored_query_result Numbers <| range X from 1 to 1000000 step 1
```

**輸出：**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

取出儲存的查詢結果：

<!-- csl -->
```kusto
stored_query_result("Numbers")
```

**輸出：**

| X |
|---|
| 1 |
| 2 |
| 3 |
| ... |

### <a name="pagination"></a>分頁

在過去七天內依 Ad network 和 day 取出點擊次數：

<!-- csl -->
```kusto
.set stored_query_result DailyClicksByAdNetwork7Days with (previewCount = 100) <|
Events
| where Timestamp > ago(7d)
| where EventType == 'click'
| summarize Count=count() by Day=bin(Timestamp, 1d), AdNetwork
| order by Count desc
| project Num=row_number(), Day, AdNetwork, Count
```

**輸出：**

| 數量 | 天 | AdNetwork | Count |
|-----|-----|-----------|-------|
| 1 | 2020-01-01 00：00：00.0000000 | NeoAds | 1002 |
| 2 | 2020-01-01 00：00：00.0000000 | HighHorizons | 543 |
| 3 | 2020-01-01 00：00：00.0000000 | PieAds | 379 |
| ... | ... | ... | ... |

取出下一個頁面：

<!-- csl -->
```kusto
stored_query_result("DailyClicksByAdNetwork7Days")
| where Num between(100 .. 200)
```

**輸出：**

| 數量 | 天 | AdNetwork | 計數 |
|-----|-----|-----------|-------|
| 100 | 2020-01-01 00：00：00.0000000 | CoolAds | 301 |
| 101 | 2020-01-01 00：00：00.0000000 | DreamAds | 254 |
| 102 | 2020-01-02 00：00：00.0000000 | SuperAds | 123 |
| ... | ... | ... | ... |

## <a name="control-commands"></a>控制命令

### <a name="show-stored_query_results"></a>。顯示 stored_query_results

顯示使用中預存查詢結果的資訊。

>[!NOTE]
> * 具有 `DatabaseAdmin` 或許可權的使用者 `DatabaseMonitor` 可以檢查資料庫內容中的使用中預存查詢結果是否存在。
> * 具有 `DatabaseUser` 或許可權的使用者 `DatabaseViewer` 可以檢查其主體所建立的使用中預存查詢結果是否存在。

#### <a name="syntax"></a>語法

`.show` `stored_query_results`

#### <a name="returns"></a>傳回

| StoredQueryResultId | 名稱 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 事件 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14：26：49.6971487 | 2020-10-08 14：26：49.6971487 |

### <a name="drop-stored_query_result"></a>。 drop stored_query_result

刪除目前資料庫中目前的主體所建立的使用中預存查詢結果。

#### <a name="syntax"></a>語法

`.drop``stored_query_result` *StoredQueryResultName*

`Database Viewer` 需要有許可權才能叫用此命令。

#### <a name="returns"></a>傳回

傳回已刪除之預存查詢結果的相關資訊，例如：

| StoredQueryResultId | 名稱 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 事件 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14：26：49.6971487 | 2020-10-08 14：26：49.6971487 |

### <a name="drop-stored_query_results"></a>。 drop stored_query_results

刪除指定的主體在目前資料庫中建立的使用中預存查詢結果。

`Database Admin` 需要有許可權才能叫用此命令。

#### <a name="syntax"></a>語法

`.drop``stored_query_results` `created-by` *PrincipalName*

#### <a name="returns"></a>傳回

傳回已刪除之預存查詢結果的資訊。

範例：

<!-- csl -->
```kusto
.drop stored_query_results by user 'aadapp=c28e9b80-2808-bed525fc0fbb'
```

| StoredQueryResultId | 名稱 | DatabaseName | PrincipalIdentity | SizeInBytes | RowCount | CreatedOn | ExpiresOn |
| ------------------- | ---- | ------------ | ----------------- | ----------- | -------- | --------- | --------- |
| c522ada3-e490-435a-a8b1-e10d00e7d5c2 | 事件 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 104372 | 1000000 | 2020-10-07 14：26：49.6971487 | 2020-10-08 14：26：49.6971487 |
| 571f1a76-f5a9-49d4-b339-ba7caac19b46 | 追蹤 | TestDB | aadapp = c28e9b80-2808-bed525fc0fbb | 5212 | 100000 | 2020-10-07 14：31：01.8271231| 2020-10-08 14：31：01.8271231 |
