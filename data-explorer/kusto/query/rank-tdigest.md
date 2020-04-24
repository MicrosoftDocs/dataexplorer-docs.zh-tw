---
title: rank_tdigest() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的rank_tdigest()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: ea24213b0ca673c39f399c3a12cc54cd7d7f47d5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510535"
---
# <a name="rank_tdigest"></a>rank_tdigest()

計算集中的值的近似排名。 集中`v``S`的值排名定義為小於或等於的成員`S`的計數`v``S`, 由其 tdigest 表示。

**語法**

`rank_tdigest(`*TDigest* `,` *Expr*`)`

**引數**

* *TDigest*: 由[tdigest()](tdigest-aggfunction.md)或[tdigest_merge()](tdigest-merge-aggfunction.md)產生的運算式
* *Expr*: 表示用於排名計算的值的運算式。

**傳回**

數據集中每個值的排名。

**技巧**

1) 要獲取其排名的值必須與 tdigest 的類型相同。

**範例**

在排序列表 (1-1000) 中,685 的排名是索引:

```kusto
range x from 1 to 1000 step 1
| summarize t_x=tdigest(x)
| project rank_of_685=rank_tdigest(t_x, 685)
```

|`rank_of_685`|
|-------------|
|`685`        |

此查詢計算值 4490$ 的所有損壞屬性成本的排名:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project rank_of_4490=rank_tdigest(tdigestRes, 4490) 

```

|`rank_of_4490`|
|--------------|
|`50207`       |

取得排名的估計百分比(除以設定大小):

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty), count()
| project rank_tdigest(tdigestRes, 4490) * 100.0 / count_

```

|`Column1`         |
|------------------|
|`85.0015237192293`|


損壞財產損失的百分位85是4490美元:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|`percentile_tdigest_tdigestRes`|
|-------------------------------|
|`4490`                         |


