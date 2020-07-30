---
title: 範例運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的範例運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: b5d0624504744bb28dfdb68ee27c48b2119242b8
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351500"
---
# <a name="sample-operator"></a>sample 運算子

從輸入資料表傳回指定數目的亂數據列。

```kusto
T | sample 5
```

## <a name="syntax"></a>語法

_T_ `| sample` _NumberOfRows_

## <a name="arguments"></a>引數

- _NumberOfRows_：要傳回的_T_資料列數目。 您可以指定任何數值運算式。

**備註**

- `sample`適用于速度，而不是平均分佈值。 具體來說，這表示如果在兩個不同大小的資料集（例如 `union` or 運算子）後面使用運算子之後，它就不會產生 ' 公平 ' 結果 `join` 。 建議在 `sample` 資料表參考和篩選準則後面使用 right。

- `sample`是不具決定性的運算子，而且每次在查詢期間評估時，都會傳回不同的結果集。 例如，下列查詢會產生兩個不同的資料列（即使有一個會預期傳回相同的資料列兩次）。

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

為了確保上述範例 `_sample` 只會計算一次，您可以使用[具體化（）](./materializefunction.md)函數：

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**提示**

- 如果您想要對特定百分比的資料進行取樣（而不是指定的資料列數目），您可以使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

- 如果您想要對索引鍵（而非資料列）進行取樣（例如，範例10識別碼並取得這些識別碼的所有資料列），您可以 [`sample-distinct`](./sampledistinctoperator.md) 搭配 `in` 運算子使用。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
