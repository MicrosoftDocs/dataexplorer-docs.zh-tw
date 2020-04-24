---
title: 範例運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的範例運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 757830bde0c56ac727d5240c01ca4768eab28877
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510025"
---
# <a name="sample-operator"></a>sample 運算子

從輸入表中返回指定數量的隨機行。

```kusto
T | sample 5
```

**語法**

_T_ `| sample` _行數_

**引數**

- _行數_:要返回的_T_行數。 您可以指定任何數字運算式。

**注意事項**

- `sample`是面向速度的,而不是均勻的值分佈。 具體來說,這意味著如果使用運算元將 2 個不同大小的數據集(如`union``join`或運算符)結合在一起,則不會生成"公平"結果。 建議在表引用和篩選器`sample`之後直接使用。

- `sample`是非確定性運算符,每次在查詢期間計算結果時都會返回不同的結果集。 例如,以下查詢生成兩個不同的行(即使預期返回同一行兩次)。

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

為了確保在上面`_sample`的範例中計算一次,可以使用[具體函數](./materializefunction.md):

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**技巧**

- 如果要對某一百分比的數據進行採樣(而不是指定數量的行),可以使用

```kusto
StormEvents | where rand() < 0.1
```

- 如果要對鍵而不是行進行採樣(例如 - 範例 10 個 Id 並取得[`sample-distinct`](./sampledistinctoperator.md)這些 Id`in`的所有行), 則可以使用 運算符組合使用。

**範例**

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```