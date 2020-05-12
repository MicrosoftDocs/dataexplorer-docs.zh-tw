---
title: percentile_tdigest （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 percentile_tdigest （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: f2e5e4aca145e0d78baddd7b1e34ab3ce6d047d1
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224997"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

計算 `tdigest` 結果（由[tdigest （）](tdigest-aggfunction.md)或[tdigest_merge （）](tdigest-merge-aggfunction.md)）產生的百分位數結果

**語法**

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *typeLiteral*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*] .。。[ `,` *PercentileN*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*動態陣列*`)`

**引數**

* *Expr*：所產生的運算式， [`tdigest`](tdigest-aggfunction.md) 或[tdigest_merge （）](tdigest-merge-aggfunction.md)。
* *百分*位數是指定百分位數的 double 常數。
* *typeLiteral*：選擇性的類型常值（例如， `typeof(long)` ）。 如果有提供，結果集會是這個型別。 
* *動態陣列*：整數或浮點數的動態陣列中的百分位數清單。

**傳回**

中每個值的百分位數/percentilesw 值 *`Expr`* 。

**提示**

* 函式必須至少接收一個百分比（而且可能更多），請參閱上述語法： *Percentile1* [ `,` *Percentile2*] .。。[ `,` *PercentileN*]），而結果會是包含結果的動態陣列。 （例如 [`percentiles()`](percentiles-aggfunction.md) ）
  
* 如果只提供一個百分比，而且也提供了類型，則結果會是與該百分比相同的類型所提供的資料行。 在此情況下，所有函式都 `tdigest` 必須屬於該類型。

* 如果 *`Expr`* 包含 `tdigest` 不同類型的函式，請不要提供類型。 結果會是動態類型。 請參閱以下範例。

**範例**

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentile_tdigest(tdigestRes, 100, typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|0|
|62000000|
|110000000|
|1200000|
|250000|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| project percentiles_array_tdigest(tdigestRes, range(0, 100, 50), typeof(int))
```

|percentile_tdigest_tdigestRes|
|---|
|[0，0，0]|
|[0，0，62000000]|
|[0，0，110000000]|
|[0，0，1200000]|
|[0，0，250000]|


```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty) by State
| union (StormEvents | summarize tdigestRes = tdigest(EndTime) by State)
| project percentile_tdigest(tdigestRes, 100)
```

|percentile_tdigest_tdigestRes|
|---|
|[0]|
|[62000000]|
|["2007-12-20T11：30： 00.0000000 Z"]|
|["2007-12-31T23：59： 00.0000000 Z"]|
