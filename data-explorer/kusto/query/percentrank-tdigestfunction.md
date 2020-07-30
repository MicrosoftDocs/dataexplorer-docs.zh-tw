---
title: percentrank_tdigest （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 percentrank_tdigest （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 33eb35e51f403a3c0b7a2f030604b12c705221ea
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346162"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

計算集合中值的近似順位，其中 rank 會以集合大小的百分比表示。
這個函數可以視為百分位數的反向。

## <a name="syntax"></a>語法

`percentrank_tdigest(`*TDigest* `,`*Expr*`)`

## <a name="arguments"></a>引數

* *TDigest*：由[TDigest （）](tdigest-aggfunction.md)或[tdigest_merge （）](tdigest-merge-aggfunction.md)所產生的運算式。
* *Expr*：代表要用於百分比排名計算的值運算式。

## <a name="returns"></a>傳回

資料集中值的百分比次序。

**提示**

1) 第二個參數的類型和中的元素類型 `tdigest` 應該相同。

2) 第一個參數應該是由[TDigest （）](tdigest-aggfunction.md)或[tdigest_merge （）](tdigest-merge-aggfunction.md)所產生的 TDigest

## <a name="examples"></a>範例

取得值為 $4490 之損毀屬性的 percentrank_tdigest （）為 ~ 85%：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


在損毀屬性上使用百分位數85應該會提供 $4490：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
