---
title: make_list （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make_list （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: c75924ed450b2995f2d35d206951adf05aecec0e
ms.sourcegitcommit: fb54d71660391a63b0c107a9703adea09bfc7cb9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86946116"
---
# <a name="make_list-aggregation-function"></a>make_list （）（彙總函式）

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。

* 只能在[匯總](summarizeoperator.md)的內容中使用

## <a name="syntax"></a>語法

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* *MaxSize*是傳回的最大專案數目的選擇性整數限制（預設為*1048576*）。 MaxSize 值不能超過1048576。

> [!NOTE]
> 此函式的舊版和過時變體： `makelist()` 具有*MaxSize* = 128 的預設限制。

## <a name="returns"></a>傳回

傳回群組中 Expr** 所有值的 `dynamic` (JSON) 陣列。
如果 `summarize` 未排序運算子的輸入，則產生之陣列中的專案順序會是未定義的。
如果對運算子的輸入 `summarize` 進行排序，則產生之陣列中的專案順序會追蹤輸入的內容。

> [!TIP]
> 使用 [`mv-apply`](./mv-applyoperator.md) 運算子，依某個索引鍵建立已排序的清單。 請參閱[此處的](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key)範例。

## <a name="examples"></a>範例

### <a name="one-column"></a>一個資料行

最簡單的範例是從單一資料行中建立一個清單：

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
|[「三角形」、「正方形」、「矩形」、「五方」、「六邊形」、「heptagon」、「octogon」、「nonagon」、「decagon」」|

### <a name="using-the-by-clause"></a>使用 ' by ' 子句

在下列查詢中，您會使用子句進行群組 `by` ：

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
|false|["三角形"，"五個"，"heptagon"，"nonagon"]|
|true|["正方形"，"rectangle"，"六邊形"，"octogon"，"decagon"]|

### <a name="packing-a-dynamic-object"></a>封裝動態物件

您可以在資料行中封裝動態物件，然後[再將其](./packfunction.md)列出，如下列查詢所示：

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
|false|[{"name"： "三角形"，"sideCount"： 3}，{"name"： "五個"，"sideCount"： 5}，{"name"： "heptagon"，"sideCount"： 7}，{"name"： "nonagon"，"sideCount"： 9}]|
|true|[{"name"： "方形"，"sideCount"： 4}，{"name"： "rectangle"，"sideCount"： 4}，{"name"： "六邊形"，"sideCount"： 6}，{"name"： "octogon"，"sideCount"： 8}，{"name"： "decagon"，"sideCount"： 10}]|

## <a name="see-also"></a>另請參閱

[`make_list_if`](./makelistif-aggfunction.md)運算子類似于 `make_list` ，不同之處在于它也接受述詞。
