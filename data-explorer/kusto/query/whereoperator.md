---
title: 其中運算符(已、包含、開頭、結束、匹配正則運算式) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的運算符(包含、包含、開頭、結束與正則運算式)的匹配位置。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e1486691740600fb5de4316982e8d43b13ce3a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504296"
---
# <a name="where-operator-has-contains-startswith-endswith-matches-regex"></a>其中運算符(有,包含,開始,結束,匹配正則表達式)

篩選資料表以建立滿足述詞的資料列子集。

```kusto
T | where fruit=="apple"
```

**別名**`filter`

**語法**

*T* `| where` *謂詞*

**引數**

* *T*: 要篩選其記錄的表格輸入。
* *沒有字*`boolean`:*T*T 的 欄位[的 表示式](./scalar-data-types/bool.md)。它計算*T*中的每一行。

**傳回**

Predicate** 是 `true` 之 T** 中的資料列。

**備註**空值:與空值相比,所有篩選函數都返回 false。 可以使用特殊的空感知函數來編寫將空值考慮在一起的查詢[:isnull()](./isnullfunction.md) [、isnotnotnull()](./isnotnullfunction.md),[無值 ()](./isemptyfunction.md),[非空()](./isnotemptyfunction.md)。 

**技巧**

若要取得最快效能︰

* 在資料行名稱和常數之間**使用簡單比較**。 ("Constant"表示表上的常量`now()``ago()`- 正常,也沒關係,使用[`let`語句](./letstatement.md)分配的標量值也是如此。

    例如，`where Timestamp >= ago(1d)` 比 `where floor(Timestamp, 1d) == ago(1d)` 更好。

* **最簡單的詞彙優先︰** 如果您使用 `and` 連結多個子句，請先放置只包含一個資料行的子句。 因此 `Timestamp > ago(1d) and OpId == EventId` 比反過來要好。

有關詳細資訊,請參閱[可用字串運算元](./datatypes-string-operators.md)的摘要和[可用數位運算符](./numoperators.md)的摘要。

**範例**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

記錄不超過 1 小時,並且來自名為"MyCluster"的源,並且具有兩個具有相同值的列。 

請注意，我們將兩個資料行之間的比較放在最後，因為它不能利用索引和強制執行掃描。

**範例**

```kusto
Traces | where * has "Kusto"
```

單詞"Kusto"出現在任何列中的所有行。