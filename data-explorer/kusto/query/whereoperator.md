---
title: Kusto 查詢語言中的 where 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 where 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7dc9d7166a1f286e14c81f269f32f894cbe9ff9d
ms.sourcegitcommit: da7c699bb62e1c4564f867d4131d26286c5223a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404184"
---
# <a name="where-operator"></a>where 運算子

篩選資料表以建立滿足述詞的資料列子集。

```kusto
T | where fruit=="apple"
```

**別名**`filter`

**語法**

*T*述詞 `| where` *Predicate*

**引數**

* *T*：要篩選其記錄的表格式輸入。
* *Predicate*述詞： `boolean` *T*之資料行的[運算式](./scalar-data-types/bool.md)。它會針對*T*中的每個資料列進行評估。

**傳回**

Predicate** 是 `true` 之 T** 中的資料列。

**附注**Null 值：相較于 null 值，所有篩選函數都會傳回 false。 您可以使用特殊的 null 感知函數來撰寫查詢，將 null 值納入考慮： [isnull （）](./isnullfunction.md)、 [isnotnull （）](./isnotnullfunction.md)、 [isempty （）](./isemptyfunction.md)、 [isnotempty （）](./isnotemptyfunction.md)。 

**提示**

若要取得最快效能︰

* 在資料行名稱和常數之間**使用簡單比較**。 （' 常數 ' 表示資料表常數 `now()` ，因此和 `ago()` 都是正常的，因此是使用[ `let` 語句](./letstatement.md)指派的純量值）。

    例如，`where Timestamp >= ago(1d)` 比 `where floor(Timestamp, 1d) == ago(1d)` 更好。

* **最簡單的詞彙優先︰** 如果您使用 `and` 連結多個子句，請先放置只包含一個資料行的子句。 因此 `Timestamp > ago(1d) and OpId == EventId` 比反過來要好。

如需詳細資訊，請參閱[可用的字串運算子](./datatypes-string-operators.md)摘要和[可用數值運算子](./numoperators.md)的摘要。

**範例**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

這個範例會抓取不超過1小時的記錄、來自稱為的來源 `MyCluster` ，而且有兩個相同值的資料行。 

請注意，我們會在最後兩個數據行之間進行比較，因為它無法使用索引並強制執行掃描。

**範例**

```kusto
Traces | where * has "Kusto"
```

"Kusto" 這個字會出現在任何資料行中的所有資料列。
