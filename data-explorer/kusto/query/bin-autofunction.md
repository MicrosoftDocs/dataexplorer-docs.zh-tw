---
title: bin_auto() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 bin_auto()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ebb214ae6a2676bf59a37e1e4e9cc3c085374bb3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517828"
---
# <a name="bin_auto"></a>bin_auto()

將值舍入到固定大小的"bin",控制查詢屬性提供的 bin 大小和起始點。

**語法**

`bin_auto``(`*運算式*`)`

**引數**

* *運算式*:表示要捨入的值的數字類型的標量運算式。

**用戶端要求屬性**

* `query_bin_auto_size`:指示每個條柱大小的數字文本。
* `query_bin_auto_at`: 表示*運算式*的一個值的數位文本,該值是"固定點"(即`fixed_point``bin_auto(fixed_point)`==`fixed_point`.) 的值。

**傳回**

下面`query_bin_auto_at`*表示式*的最近倍數,`query_bin_auto_at`移動, 以便被翻譯成本身.

**範例**

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|時間戳記                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |