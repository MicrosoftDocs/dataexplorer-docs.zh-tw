---
title: bin_auto （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 bin_auto （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6df5d9793f2d076eb8f97156e911fb49aba4cc9c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349154"
---
# <a name="bin_auto"></a>bin_auto()

將值向下舍入固定大小的 "bin"，以控制查詢屬性所提供的 bin 大小和起點。

## <a name="syntax"></a>語法

`bin_auto``(`*運算式*`)`

## <a name="arguments"></a>引數

* *Expression*：數數值型別的純量運算式，表示要四捨五入的值。

**用戶端要求屬性**

* `query_bin_auto_size`：指出每個 bin 大小的數值常值。
* `query_bin_auto_at`：數值常值，表示*運算式*的一個值，這是一個「固定點」（也就是值 `fixed_point` `bin_auto(fixed_point)` == `fixed_point` ）。

## <a name="returns"></a>傳回

下一個運算式的最接近倍數 `query_bin_auto_at` ，已移位，因此*Expression* `query_bin_auto_at` 會轉譯成其本身。

## <a name="examples"></a>範例

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00：05：00.0000000  | 60    |
|2017-01-01 01：05：00.0000000  | 56    |