---
title: row_number （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 row_number （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea51e6171b8a7683a0454d177dc729ed754b8896
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351585"
---
# <a name="row_number"></a>row_number()

傳回[序列化資料列集中](./windowsfunctions.md#serialized-row-set)的目前資料列索引。
根據預設，資料列索引 `1` 會針對第一個資料列開始，而且 `1` 每個額外的資料列會遞增。
（選擇性）資料列索引可以從與不同的值開始 `1` 。
此外，資料列索引可能會根據某些提供的述詞進行重設。

## <a name="syntax"></a>語法

`row_number``(`[*StartingIndex* [ `,` *重新開機*]]`)`

* *StartingIndex*是類型的常數運算式 `long` ，表示要開始的資料列索引值（或重新開機）。 預設值是 `1`。
* *Restart*是類型的選擇性引數 `bool` ，表示要將編號重新開機為*StartingIndex*值的時間。 如果未提供，則會使用的預設值 `false` 。

## <a name="returns"></a>傳回

函數會傳回目前資料列的資料列索引，做為類型的值 `long` 。

## <a name="examples"></a>範例

下列範例會傳回包含兩個數據行的資料表，第一個 `a` 資料行（）的數位從 `10` 向下到 `1` ，而第二個數據行（ `rn` ）的數位從 `1` 到 `10` ：

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

下列範例與上述範例類似，只有第二個數據行（ `rn` ）會從開始 `7` ：

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

最後一個範例會示範如何分割資料，以及如何為每個分割區的資料列編號。 在這裡，我們會將資料分割為 `Airport` ：

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

執行此查詢會產生下列結果：

機場  | 航空公司  | 離開  | 排名
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | LH       | 1           | 2
SEA      | LY       | 0           | 3
TLV      | LY       | 100         | 1
TLV      | LH       | 1           | 2