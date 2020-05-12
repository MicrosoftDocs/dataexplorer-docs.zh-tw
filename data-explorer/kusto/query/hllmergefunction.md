---
title: hll_merge （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hll_merge （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 2cddd645b0b89b3356adabae26874f93b41f8815
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226545"
---
# <a name="hll_merge"></a>hll_merge()

合併 `hll` 結果（匯總版本的純量版本 [`hll_merge()`](hll-merge-aggfunction.md) ）。

閱讀[基礎演算法（*H*Yper*l*og*l*og）和估計精確度](dcount-aggfunction.md#estimation-accuracy)的相關資訊。

**語法**

`hll_merge(`*運算式 1* `,`*運算式 2*`, ...)`

**引數**

* 具有 `hll` 要合併之值的資料行。

**傳回**

將資料行 `*Exrp1*` （ `*Expr2*` ，...）合併 `*ExprN*` 成一個值的結果 `hll` 。

**範例**

<!-- csl: https://help.kusto.windows.net:443/KustoMonitoringPersistentDatabase -->
```kusto
range x from 1 to 10 step 1 
| extend y = x + 10
| summarize hll_x = hll(x), hll_y = hll(y)
| project merged = hll_merge(hll_x, hll_y)
| project dcount_hll(merged)
```

|`dcount_hll_merged`|
|---|
|20|
