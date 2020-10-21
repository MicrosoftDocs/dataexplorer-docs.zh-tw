---
title: 'toscalar ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 toscalar ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1210a820b4b3c8790d218ba53992da0255028de2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252013"
---
# <a name="toscalar"></a>toscalar()

傳回評估運算式的純量常數值。 

此函數適用于需要暫存計算的查詢。 例如，計算事件的總計數，然後使用結果來篩選超過所有事件之特定百分比的群組。

## <a name="syntax"></a>語法

`toscalar(`*表達*`)`

## <a name="arguments"></a>引數

* *Expression*：將針對純量轉換進行評估的運算式。

## <a name="returns"></a>傳回

已評估之運算式的純量常數值。
如果結果是表格式，則會採用第一個資料行和第一個資料列進行轉換。

> [!TIP]
> 使用時，您可以使用 [let 語句](letstatement.md) 來取得查詢的可讀性 `toscalar()` 。

**備註**

`toscalar()` 可以在查詢執行期間計算固定的次數。
`toscalar()`函數無法在資料列層級的 (上套用) 的每個資料列案例。

## <a name="examples"></a>範例

評估 `Start` 、 `End` 和為純量 `Step` 常數，並使用 `range` 評估結果。

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|步驟|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|
