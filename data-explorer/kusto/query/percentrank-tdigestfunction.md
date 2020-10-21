---
title: 'percentrank_tdigest ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 percentrank_tdigest。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: dd784d8968b45a735bd2df840a09c349e2fdcbd2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249691"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

計算集合中值的大約順位，其中 rank 會以集合大小的百分比表示。
您可以將此函數視為百分位數的反向。

## <a name="syntax"></a>語法

`percentrank_tdigest(`*TDigest* `,`*Expr*`)`

## <a name="arguments"></a>引數

* *TDigest*： TDigest 所產生的運算式 [ ( # B1 ](tdigest-aggfunction.md) 或 [tdigest_merge ( # B3 ](tdigest-merge-aggfunction.md)。
* *Expr*：代表要用於計算百分比排名之值的運算式。

## <a name="returns"></a>傳回

資料集中值的百分比排名。

**提示**

1) 第二個參數的類型和中的元素類型 `tdigest` 應該相同。

2) 第一個參數應該是[TDigest ( # B1](tdigest-aggfunction.md)或[Tdigest_merge ( # B3](tdigest-merge-aggfunction.md)所產生的 TDigest

## <a name="examples"></a>範例

取得 [損毀] 屬性的 percentrank_tdigest ( # A1 （值為 $4490）是 ~ 85%：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|資料行1|
|---|
|85.0015237192293|


在損毀屬性上使用百分位數85，應提供 $4490：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|
