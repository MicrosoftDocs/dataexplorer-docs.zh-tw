---
title: atan2() - Azure 數據資源管理員 |微軟文件
description: 本文在 Azure 資料資源管理器中介紹 atan2()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8b49191c9d955cf5a91bde2032798f4703f7910
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518389"
---
# <a name="atan2"></a>atan2()

計算正 x 軸和從原點到點 (y, x) 的光線之間的角度(以弧度表示)。

**語法**

`atan2(`*y*`,`*x*`)`

**引數**

* *x*: X 座標(實數)。
* *Y*: Y 座標(實數)。

**傳回**

* 正 x 軸與從原點到點 (y, x) 的光線之間的角度(以弧度表示)。

**範例**

```kusto
print atan2_0 = atan2(1,1) // Pi / 4 radians (45 degrees)
| extend atan2_1 = atan2(0,-1) // Pi radians (180 degrees)
| extend atan2_2 = atan2(-1,0) // - Pi / 2 radians (-90 degrees)
```

|atan2_0|atan2_1|atan2_2|
|---|---|---|
|0.785398163397448|3.14159265358979|-1.5707963267949|