---
title: Kusto 查詢語言中的 where 運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 where 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512004"
---
# <a name="where-operator"></a>where 運算子

篩選資料表以建立滿足述詞的資料列子集。

```kusto
T | where fruit=="apple"
```

**別名** `filter`

## <a name="syntax"></a>語法

*T* `| where` *Predicate*

## <a name="arguments"></a>引數

* *T*：要篩選記錄的表格式輸入。
* *述詞*：*T* 之資料行的 `boolean` [運算式](./scalar-data-types/bool.md)。其會針對 *T* 中的每個資料列進行評估。

## <a name="returns"></a>傳回

Predicate 是 `true` 之 T 中的資料列。

**注意** Null 值：與 null 值比較時，所有篩選函式都會傳回 false。 您可以使用特殊 null 感知函式來撰寫可處理 null 值的查詢。

[isnull()](./isnullfunction.md)、[isnotnull()](./isnotnullfunction.md)、[isempty()](./isemptyfunction.md)、[isnotempty()](./isnotemptyfunction.md)。 

**提示**

若要取得最快效能︰

* 在資料行名稱和常數之間 **使用簡單比較**。 ('Constant' 表示資料表常數，因此 `now()` 和 `ago()` 都沒問題，並且是使用 [`let` 陳述式](./letstatement.md)指派的純量值)。

    例如，`where Timestamp >= ago(1d)` 比 `where floor(Timestamp, 1d) == ago(1d)` 更好。

* **最簡單的詞彙優先︰** 如果您使用 `and` 連結多個子句，請先放置只包含一個資料行的子句。 因此 `Timestamp > ago(1d) and OpId == EventId` 比反過來要好。

如需詳細資訊，請參閱[可用 String 運算子](./datatypes-string-operators.md)的摘要以及[可用數值運算子](./numoperators.md)的摘要。

## <a name="example-simple-comparisons-first"></a>範例：先進行簡單的比較

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

此範例擷取的記錄不超過 1 小時、來自稱為 `MyCluster` 的來源，而且有值相同的兩個資料行。 

請注意，我們將兩個資料行之間的比較放在最後，因為其無法使用索引和強制執行掃描。

## <a name="example-columns-contain-string"></a>範例：資料行包含字串

```kusto
Traces | where * has "Kusto"
```

"Kusto" 這個字出現在任何資料行中的所有資料列。
 
