---
title: 'percentile_tdigest ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 percentile_tdigest。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 7111f34fcd42e025b22960aa013310d7e4b672fd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252172"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

從結果 (計算百分位數結果， `tdigest` 由 [tdigest ( # B2 ](tdigest-aggfunction.md) 或 [tdigest_merge ( # B4 ](tdigest-merge-aggfunction.md) 所產生) 

## <a name="syntax"></a>語法

`percentile_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *typeLiteral*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*Percentile1* [ `,` *Percentile2*] .。。[ `,` *PercentileN*]`)`

`percentiles_array_tdigest(`*`Expr`*`,`*動態陣列*`)`

## <a name="arguments"></a>引數

* *Expr*：由 [`tdigest`](tdigest-aggfunction.md) 或 [tdigest_merge ( # B1 ](tdigest-merge-aggfunction.md)產生的運算式。
* *百分* 位數是指定百分位數的雙精度浮點數。
* *typeLiteral*：選擇性的型別常值 (例如 `typeof(long)`) 。 如果有提供，結果集將會是此類型。 
* *動態陣列*：整數或浮點數的動態陣列中的百分位數清單。

## <a name="returns"></a>傳回

中每個值的百分位數/percentilesw 值 *`Expr`* 。

**提示**

* 函式至少必須獲得一個百分比 (，請參閱上述語法： *Percentile1* [ `,` *Percentile2*] .。。[ `,` *PercentileN*] ) ，且結果將會是包含結果的動態陣列。  (像是 [`percentiles()`](percentiles-aggfunction.md)) 
  
* 如果只提供一個百分比，而且也提供型別，則結果將會是該百分比的結果所提供之相同類型的資料行。 在此情況下，所有函式都 `tdigest` 必須屬於該類型。

* 如果 *`Expr`* 包含 `tdigest` 不同類型的函式，則不提供類型。 結果會是動態類型。 請參閱以下範例。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
