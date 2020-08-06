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
ms.openlocfilehash: 89e9eea4e8a6a5922e9141818fc5832156ac8e72
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803551"
---
# <a name="sample-operator"></a>sample 運算子

從輸入資料表傳回指定數目的亂數據列。

```kusto
T | sample 5
```

> [!NOTE]
> * `sample`適用于速度，而不是平均分佈值。 具體來說，這表示它不會產生 ' 公平 ' 結果（如果在運算子後面使用不同大小的兩個資料集， (例如 `union` `join`) 或運算子）。 建議在 `sample` 資料表參考和篩選準則後面使用 right。
> * `sample`是不具決定性的運算子，而且每次在查詢期間評估時，都會傳回不同的結果集。 例如，下列查詢會產生兩個不同的資料列 (即使是預期會傳回相同的資料列兩次) 。

## <a name="syntax"></a>語法

*T* `| sample` *NumberOfRows*

## <a name="arguments"></a>引數

* *NumberOfRows*：要傳回的*T*資料列數目。 您可以指定任何數值運算式。

## <a name="examples"></a>範例

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

為了確保上述範例 `_sample` 只計算一次，您可以使用[具體化 ( # B1](./materializefunction.md)函數：

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

若要取樣特定百分比的資料 (，而不是指定數目的資料列) ，您可以使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

若要將索引鍵（而非資料列）取樣 (例如-範例10識別碼，並取得這些識別碼的所有資料列) 您可以 [`sample-distinct`](./sampledistinctoperator.md) 搭配 `in` 運算子使用。


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
