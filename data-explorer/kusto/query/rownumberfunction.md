---
title: 'row_number ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 row_number。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 457e9445aa113e76052b9c4d96019352215d08f9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242802"
---
# <a name="row_number"></a>row_number()

傳回 [序列化資料列集中](./windowsfunctions.md#serialized-row-set)目前資料列的索引。
依預設，資料列索引 `1` 會在第一個資料列開始，並為 `1` 每個額外的資料列遞增。
（選擇性）資料列索引可以從不同的值開始 `1` 。
此外，也可以根據某些提供的述詞來重設資料列索引。

## <a name="syntax"></a>語法

`row_number``(`[*StartingIndex* [ `,` *重新開機*]]`)`

* *StartingIndex* 是類型的常數運算式 `long` ，表示要從 (開始的資料列索引值，或重新開機以) 。 預設值為 `1`。
* *Restart* 是類型的選擇性引數 `bool` ，指出編號何時要重新開機為 *StartingIndex* 值。 如果未提供，則會使用的預設值 `false` 。

## <a name="returns"></a>傳回

函數會傳回目前資料列的資料列索引，作為類型的值 `long` 。

## <a name="examples"></a>範例

下列範例會傳回包含兩個數據行的資料表，第一個 `a` 資料行 () ，其中包含從 `10` 下到的數位 `1` ，而第二個數據行則 (`rn`) ， `1` 最多可達 `10` ：

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

下列範例類似上述範例，只有第二個數據行 (`rn`) 開始于 `7` ：

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

最後一個範例會示範如何分割資料，以及如何為每個資料分割的資料列編號。 在這裡，我們會依下列方式分割資料 `Airport` ：

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
SEA      | Lh       | 1           | 2
SEA      | LY       | 0           | 3
TLV      | LY       | 100         | 1
TLV      | Lh       | 1           | 2