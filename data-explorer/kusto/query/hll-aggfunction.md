---
title: hll() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 hll()(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/15/2020
ms.openlocfilehash: 52eac2984ed29bf8de21fb378a84b789015aa1c5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514122"
---
# <a name="hll-aggregation-function"></a>hll() (聚合函數)

計算整個組[中計數](dcount-aggfunction.md)的中間結果。 

* 只能在[匯總](summarizeoperator.md)中的聚合上下文中使用。

閱讀有關[基礎演演算法 *(H*yper*L*og*L*og) 和估計精度](dcount-aggfunction.md#estimation-accuracy)。

**語法**

`summarize``hll(` *Expr* `,` [*精度*]`)`

**引數**

* *Expr*:將用於聚合計算的運算式。 
* ** (若已指定) 會控制速度和精確度之間的平衡。
    * `0` = 最不精確但最快速的計算。 1.6% 錯誤
    * `1`• 預設值,它平衡了準確性和計算時間;約 0.8% 錯誤。
    * `2`• 準確和緩慢的計算;約 0.4% 錯誤。
    * `3`• 超精確和緩慢的計算;約 0.28% 錯誤。
    * `4`• 超精確和最慢的計算;約 0.2% 錯誤。
    
**傳回**

跨組*Expr*的不同計數的中間結果。
 
**技巧**

1) 您可以使用聚合函數[hll_merge](hll-merge-aggfunction.md)合併多個 hll 中間結果(它僅適用於 hll 輸出)。

2) 您可以使用函數[dcount_hll,](dcount-hllfunction.md)此函數會將從 hll / hll_merge聚合函數中計算 dcount。

**範例**

```kusto
StormEvents
| summarize hll(DamageProperty) by bin(StartTime,10m)

```

|StartTime|hll_DamageProperty|
|---|---|
|2007-09-18 20:00:00.0000000|[[1024,14],[-5473486921211236216,-6230876016761372746,3953448761157777955,4246796580750024372],[]]|
|2007-09-20 21:50:00.0000000|[[1024,14],[4835649640695509390],[]]|
|2007-09-29 08:10:00.0000000|[[1024,14],[4246796580750024372],[]]|
|2007-12-30 16:00:00.0000000|[[1024,14],[4246796580750024372,-8936707700542868125],[]]|