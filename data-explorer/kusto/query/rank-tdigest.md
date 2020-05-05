---
title: rank_tdigest （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 rank_tdigest （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: a849cd496d41ad473768b3f267639eaf8c467880
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741772"
---
# <a name="rank_tdigest"></a>rank_tdigest()

計算集合中值的近似順位。 `v`集合`S`中的值次序定義為較小或等於的成員`S`計數`v`， `S`由其`tdigest`代表。

**語法**

`rank_tdigest(`*`TDigest`*`,` *`Expr`*`)`

**引數**

* *TDigest*：由[TDigest （）](tdigest-aggfunction.md)或[tdigest_merge （）](tdigest-merge-aggfunction.md)所產生的運算式
* *Expr*：代表要用於排序計算之值的運算式。

**傳回**

資料集中的 rank foreach 值。

**提示**

1) 您想要取得其順位的值，其類型必須與相同`tdigest`。

**範例**

在排序清單（1-1000）中，685的順位是其索引：

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

此查詢會計算所有損害屬性成本的值 $4490 排名：

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

取得順位的預估百分比（除以集合大小）：

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


損毀屬性成本的百分位數85為 $4490：

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


