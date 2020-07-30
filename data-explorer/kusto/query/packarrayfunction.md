---
title: pack_array （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 pack_array （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad6403a70a8f71eee05bc56789cae2d6287a6605
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346519"
---
# <a name="pack_array"></a>pack_array()

將所有輸入值封裝到動態陣列中。

## <a name="syntax"></a>語法

`pack_array(`*運算式 1* `[` ， ` *Expr2*]` ） '

## <a name="arguments"></a>引數

* *運算式 1 .。。N*：要封裝到動態陣列中的輸入運算式。

## <a name="returns"></a>傳回

動態陣列，其中包含運算式1、運算式2、...、ExprN 的值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
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

|Column1|
|---|
|[1，"2"，"00：00： 02"]|
|[2，"4"，"00：00： 04"]|
|[3，"6"，"00：00： 06"]|
