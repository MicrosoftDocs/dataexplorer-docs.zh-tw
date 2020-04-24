---
title: percentrank_tdigest() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 percentrank_tdigest()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
ms.openlocfilehash: fe356ddb2e6301bbb283d2e6aa59b5c98f8bf3fe
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511181"
---
# <a name="percentrank_tdigest"></a>percentrank_tdigest()

計算一集中的值的近似排名,其中排名以集大小的百分比表示。 此功能可以視為百分位數的反數。

**語法**

`percentrank_tdigest(`*TDigest* `,` *Expr*`)`

**引數**

* *TDigest*: 由[tdigest()](tdigest-aggfunction.md)或[tdigest_merge()](tdigest-merge-aggfunction.md)生成的運算式。
* *Expr*: 表示用於百分比排名計算的值的運算式。

**傳回**

數據集中值的百分比排名。

**技巧**

1) 第二個參數的類型和 tdigest 中的元素類型應相同。

2) 第一個參數應該是[tdigest,由 tdigest()](tdigest-aggfunction.md)或[tdigest_merge()](tdigest-merge-aggfunction.md)產生

**範例**

獲得價值4490美元的損壞財產的percentrank_tdigest()是+85%:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentrank_tdigest(tdigestRes, 4490)

```

|Column1|
|---|
|85.0015237192293|


使用百分85超過損壞財產應給予4490美元:

```kusto
StormEvents
| summarize tdigestRes = tdigest(DamageProperty)
| project percentile_tdigest(tdigestRes, 85, typeof(long))

```

|percentile_tdigest_tdigestRes|
|---|
|4490|