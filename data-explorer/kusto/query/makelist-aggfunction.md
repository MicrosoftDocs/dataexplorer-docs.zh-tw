---
title: 'make_list ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) make_list。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 7f17302475221bb259e6717987f7d31e96d7c118
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366022"
---
# <a name="make_list-aggregation-function"></a>make_list ( # A1 (彙總函式) 

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中

## <a name="syntax"></a>語法

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* *MaxSize* 是選擇性的整數限制， (預設值為 *1048576*) 時傳回的元素數目上限。 MaxSize 值不能超過1048576。

> [!NOTE]
> 此函式的舊版和過時變數：的 `makelist()` 預設限制為 *MaxSize* = 128。

## <a name="returns"></a>傳回

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序是未定義的。
如果排序運算子的輸入 `summarize` ，則產生的陣列中的元素順序會追蹤輸入的專案順序。

> [!TIP]
> 使用 [`mv-apply`](./mv-applyoperator.md) 運算子依某些索引鍵建立已排序的清單。 請參閱[此處的](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key)範例。

## <a name="examples"></a>範例

### <a name="one-column"></a>一個資料行

最簡單的範例是將清單從單一資料行中取出：

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name)
```

|mylist|
|---|
|[「三角形」、「方形」、「矩形」、「五形」、「六邊形」、「heptagon」、「octogon」、「nonagon」、「decagon」」|

### <a name="using-the-by-clause"></a>使用 ' by ' 子句

在下列查詢中，您會使用子句進行分組 `by` ：

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| summarize mylist = make_list(name) by isEvenSideCount = sideCount % 2 == 0
```

|mylist|isEvenSideCount|
|---|---|
|false|[「三角形」、「五邊」、「heptagon」、「nonagon」|
|true|["方形"、"rectangle"、"六邊形"、"octogon"、"decagon"]|

### <a name="packing-a-dynamic-object"></a>封裝動態物件

您可以 [先將動態](./packfunction.md) 物件封裝在資料行中，再將它移出資料行，如下列查詢所示：

```kusto
let shapes = datatable (name: string, sideCount: int)
[
    "triangle", 3,
    "square", 4,
    "rectangle", 4,
    "pentagon", 5,
    "hexagon", 6,
    "heptagon", 7,
    "octogon", 8,
    "nonagon", 9,
    "decagon", 10
];
shapes
| extend d = pack("name", name, "sideCount", sideCount)
| summarize mylist = make_list(d) by isEvenSideCount = sideCount % 2 == 0
```

|mylist|isEvenSideCount|
|---|---|
|false|[{"name"： "三角形"，"sideCount"： 3}，{"name"： "正五"，"sideCount"： 5}，{"name"： "heptagon"，"sideCount"： 7}，{"name"： "nonagon"，"sideCount"： 9}]|
|true|[{"name"： "方形"，"sideCount"： 4}，{"name"： "rectangle"，"sideCount"： 4}，{"name"： "六邊形"，"sideCount"： 6}，{"name"： "octogon"，"sideCount"： 8}，{"name"： "decagon"，"sideCount"： 10}]|

## <a name="see-also"></a>另請參閱

[`make_list_if`](./makelistif-aggfunction.md) 運算子與相同 `make_list` ，但它也接受述詞。
