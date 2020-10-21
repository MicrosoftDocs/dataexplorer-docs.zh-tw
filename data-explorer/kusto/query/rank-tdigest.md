---
title: 'rank_tdigest ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 rank_tdigest。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: bc0fff9d70c8260781332be61c701aaaca364555
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244847"
---
# <a name="rank_tdigest"></a>rank_tdigest()

計算集合中值的大致順位。 集合中值的次序 `v` `S` 定義為 `S` 小於或等於之成員的計數，以 `v` `S` 其表示 `tdigest` 。

## <a name="syntax"></a>語法

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

## <a name="arguments"></a>引數

* *TDigest*： TDigest 所產生的運算式 [ ( # B1 ](tdigest-aggfunction.md) 或 [tdigest_merge ( # B3 ](tdigest-merge-aggfunction.md)
* *Expr*：運算式，表示要用於排名計算的值。

## <a name="returns"></a>傳回

資料集中的排名 foreach 值。

**提示**

1) 您要取得其排名的值必須與相同的類型 `tdigest` 。

## <a name="examples"></a>範例

在排序的清單中 (1-1000) ，685的次序是其索引：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

此查詢會計算所有損毀屬性成本的值 $4490 排名：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

藉由除以 set 大小) ，取得排名 (的估計百分比：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


損毀屬性成本的百分位數85為 $4490：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


