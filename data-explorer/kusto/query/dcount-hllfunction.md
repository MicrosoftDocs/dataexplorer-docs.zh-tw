---
title: dcount_hll() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的dcount_hll()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: a0c921efa90f5d66fe42fa6ee872204b5bb399cd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516145"
---
# <a name="dcount_hll"></a>dcount_hll()

從 hll 結果(由[hll](hll-aggfunction.md)或[hll_merge](hll-merge-aggfunction.md)生成)計算 dcount。

閱讀有關[基礎演演算法 *(H*yper*L*og*L*og) 和估計精度](dcount-aggfunction.md#estimation-accuracy)。

**語法**

`dcount_hll(`*Expr*`)`

**引數**

* *Expr*: 由[hll 或 hll-merge](hll-merge-aggfunction.md)產生的運算式[hll](hll-aggfunction.md)

**傳回**

*Expr*中每個值的不同計數

**範例**

```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|