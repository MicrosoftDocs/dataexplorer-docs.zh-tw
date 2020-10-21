---
title: 'bin_auto ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 bin_auto。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dba71c3b9b52a4edaf3a9b1260f56fc94eb935e3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245444"
---
# <a name="bin_auto"></a>bin_auto()

將值向下舍入為固定大小的 "bin"，可控制查詢屬性所提供的 bin 大小和起點。

## <a name="syntax"></a>語法

`bin_auto` `(` *運算式* `)`

## <a name="arguments"></a>引數

* *運算式*：數數值型別的純量運算式，表示要四捨五入的值。

**用戶端要求屬性**

* `query_bin_auto_size`：表示每個 bin 大小的數值常值。
* `query_bin_auto_at`：表示一個 *運算式* 值的數值常值，也就是「固定點」 (也就是值 `fixed_point` `bin_auto(fixed_point)` == `fixed_point` 。 ) 

## <a name="returns"></a>傳回

下方運算式的最接近倍數 `query_bin_auto_at` 會移位， *Expression*以便 `query_bin_auto_at` 轉譯成本身。

## <a name="examples"></a>範例

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|時間戳記                    | count_|
|-----------------------------|-------|
|2017-01-01 00：05：00.0000000  | 60    |
|2017-01-01 01：05：00.0000000  | 56    |