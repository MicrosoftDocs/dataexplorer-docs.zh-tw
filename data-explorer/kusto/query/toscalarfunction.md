---
title: toscalar （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 toscalar （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 649d09fcf6d228714fdf20b40c81b2a2552374e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340253"
---
# <a name="toscalar"></a>toscalar()

傳回已評估運算式的純量常數值。 

此函數適用于需要分段計算的查詢。 例如，計算事件的總計數，然後使用結果來篩選超過所有事件的特定百分比的群組。

## <a name="syntax"></a>語法

`toscalar(`*運算式*`)`

## <a name="arguments"></a>引數

* *Expression*：將針對純量轉換進行評估的運算式。

## <a name="returns"></a>傳回

已評估運算式的純量常數值。
如果結果是表格式，則會採用第一個資料行和第一個資料列來進行轉換。

> [!TIP]
> 當您使用時，可以使用[let 語句](letstatement.md)來取得查詢的可讀性 `toscalar()` 。

**備註**

`toscalar()`可以在查詢執行期間計算固定次數。
`toscalar()`無法在資料列層級（每個資料列的案例）上套用函數。

## <a name="examples"></a>範例

`Start`將、 `End` 和評估為純量 `Step` 常數，並使用結果進行 `range` 評估。

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|開始|end|步驟|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|
