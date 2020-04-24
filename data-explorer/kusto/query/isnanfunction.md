---
title: isnan() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 isnan()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 123d9cd32d645bb1225983138973a17b6bb9ecf3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513561"
---
# <a name="isnan"></a>isnan()

返回輸入是否不是數位 (NaN) 值。  

**語法**

`isnan(`*X.*`)`

**引數**

* *x*: 實數。

**傳回**

如果 x 為 NaN,則為非零值(true);和零(假)否則。

**另請參閱**

* 有關檢查值是否為空,請參閱[是 null()](isnullfunction.md)。
* 有關檢查值是否有限,請參閱[有限()](isfinitefunction.md)。
* 有關檢查值是否無限,請參閱[isinf()](isinffunction.md)。

**範例**

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|