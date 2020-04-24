---
title: 有限() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的有限()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a17a39cce91fe039b2cf55cc5c98dba111cc334
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513595"
---
# <a name="isfinite"></a>isfinite()

返回輸入是否為有限值(既不是無限值也不是 NaN)。

**語法**

`isfinite(`*X.*`)`

**引數**

* *x*: 實數。

**傳回**

如果 x 是有限的,則為非零值(true);和零(假)否則。

**另請參閱**

* 有關檢查值是否為空,請參閱[是 null()](isnullfunction.md)。
* 有關檢查值是否無限,請參閱[isinf()](isinffunction.md)。
* 有關檢查值是否為 NaN(不是數位),請參閱[isnan()](isnanfunction.md)。

**範例**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|是有限的|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|