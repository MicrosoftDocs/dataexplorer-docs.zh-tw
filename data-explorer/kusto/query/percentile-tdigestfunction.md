---
title: percentile_tdigest() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 percentile_tdigest()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: 655a0693b8e04b1230f6b9e8fe247bd2d77b7ac6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511232"
---
# <a name="percentile_tdigest"></a>percentile_tdigest()

計算從t消化結果(由[tdigest()](tdigest-aggfunction.md)或[tdigest_merge()](tdigest-merge-aggfunction.md)生成的百分位結果。

**語法**

`percentile_tdigest(`*Expr* `,` *百分位數1* =`,` *類型字形*|`)`

`percentiles_array_tdigest(`*Expr* `,` *百分位數1* =`,` *百分位數 2*= ...[`,` *百分位數]*`)`

`percentiles_array_tdigest(`*Expr* `,` *動態陣列*`)`

**引數**

* *Expr*: 由[tdigest](tdigest-aggfunction.md)或[tdigest_merge()](tdigest-merge-aggfunction.md)生成的運算式。
* *百分位數*是指定百分位數的雙常量。
* *字形*類型 : 可選擇型態`typeof(long)`文字(例如, 如果提供,結果集將為此類型。 
* *動態陣列*:整數或浮點數動態陣列中的百分位數清單

**傳回**

*Expr*中每個值的百分位數/百分位數值。

**技巧**

1) 函數必須接收至少 1% (也許更多,請參閱上面的語法:*百分位數1* =`,` *百分位數2*= ...[`,` *百分位數N*])結果將是一個包含結果的動態數位。 ([`percentiles()`](percentiles-aggfunction.md)如 )
  
2) 如果只提供了百分之一,並且也提供了類型,則結果將是提供該百分比結果的相同類型的列(在這種情況下,所有 tdigest 都必須是該類型)。

3) 如果*Expr*包含不同類型的 tdigest,則不提供類型,結果將是動態類型。 (請參閱下面的示例)。

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
|[0,0,0]|
|[0,0,62000000]|
|[0,0,110000000]|
|[0,0,1200000]|
|[0,0,250000]|


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
|[2007-12-20T11:30:000000Z]。|
|[2007-12-31T23:59:0000000Z"]|