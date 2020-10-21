---
title: 'pack_array ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 pack_array。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3e9fbe7c11aeffbdc0274d4433eddd9a5463b262
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248691"
---
# <a name="pack_array"></a>pack_array()

將所有輸入值封裝至動態陣列中。

## <a name="syntax"></a>語法

`pack_array(`*運算式 1-1* `[` ， ` *Expr2*]`) '

## <a name="arguments"></a>引數

* *運算式1：N*：要封裝至動態陣列中的輸入運算式。

## <a name="returns"></a>傳回

動態陣列，其中包含值1：//、運算式2、...、ExprN 的值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|資料行1|
|---|
|[1，2，4]|
|[2，4，8]|
|[3，6，12]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|資料行1|
|---|
|[1，"2"，"00：00： 02"]|
|[2，"4"，"00：00： 04"]|
|[3，"6"，"00：00： 06"]|
