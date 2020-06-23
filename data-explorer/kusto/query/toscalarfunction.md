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
ms.openlocfilehash: 15d9056ec21eb6f25ccbc985d659f310d670f02d
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133408"
---
# <a name="toscalar"></a>toscalar()

傳回已評估運算式的純量常數值。 

此函數適用于需要分段計算的查詢。 例如，計算事件的總計數，然後使用結果來篩選超過所有事件的特定百分比的群組。

**語法**

`toscalar(`*運算式*`)`

**引數**

* *Expression*：將針對純量轉換進行評估的運算式。

**傳回**

已評估運算式的純量常數值。
如果結果是表格式，則會採用第一個資料行和第一個資料列來進行轉換。

> [!TIP]
> 當您使用時，可以使用[let 語句](letstatement.md)來取得查詢的可讀性 `toscalar()` 。

**注意事項**

`toscalar()`可以在查詢執行期間計算固定次數。
`toscalar()`無法在資料列層級（每個資料列的案例）上套用函數。

**範例**

`Start`將、 `End` 和評估為純量 `Step` 常數，並使用結果進行 `range` 評估。

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
