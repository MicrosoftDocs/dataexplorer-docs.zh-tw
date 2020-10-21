---
title: 'atan2 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 atan2 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1229ff1476afe2863f07cfc0ff7aecffadd5867f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252770"
---
# <a name="atan2"></a>atan2()

將正 X 軸和光線之間的角度（以弧度為單位）從原點算到 (y x) 的點。

## <a name="syntax"></a>語法

`atan2(`*y* `,`*x*`)`

## <a name="arguments"></a>引數

* *x*： x 座標 (實數) 。
* *y*： y 座標 (實數) 。

## <a name="returns"></a>傳回

* 從原點到點 (y x) 之間的角度（以弧度為單位）。

## <a name="examples"></a>範例

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|（3.14159265358979|-1.5707963267949|