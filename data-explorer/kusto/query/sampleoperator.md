---
title: 範例運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的範例操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 35495b9541f7dac35bb1aa45cbc435e7f3fedfad
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242719"
---
# <a name="sample-operator"></a>sample 運算子

從輸入資料表傳回指定數目的亂數據列。

```kusto
T | sample 5
```

> [!NOTE]
> * `sample` 適用于速度，而不是平均分佈值。 具體來說，這表示如果在運算子之後使用了不同大小的聯集2資料集 (例如 `union` 或運算子) ，它就不會產生「公平」結果 `join` 。 建議您在 `sample` 資料表參考和篩選準則之後使用 right。
> * `sample` 是不具決定性的運算子，而且每次在查詢期間評估時，都會傳回不同的結果集。 例如，下列查詢會產生兩個不同的資料列 (即使一次預期會傳回相同的資料列) 。

## <a name="syntax"></a>語法

*T* `| sample` *NumberOfRows*

## <a name="arguments"></a>引數

* *NumberOfRows*：要傳回的 *T* 的資料列數目。 您可以指定任何數值運算式。

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

為了確保在上述範例中， `_sample` 只會計算一次，一個可以使用 [具體化 ( # B1 ](./materializefunction.md) 函數：

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

若要 (資料的特定百分比，而不是) 的指定資料列數目，您可以使用

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

為範例索引鍵而非資料列 (例如範例10識別碼，並取得這些識別碼的所有資料列) 您可以 [`sample-distinct`](./sampledistinctoperator.md) 搭配運算子使用 `in` 。


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
