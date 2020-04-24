---
title: isinf() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 isinf()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d93697890ee05cabf9ca1830ac047d90d8c9e844
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513578"
---
# <a name="isinf"></a>isinf()

返回輸入是無限(正值還是負值)。  

**語法**

`isinf(`*X.*`)`

**引數**

* *x*: 實數。

**傳回**

如果 x 為正或負無限,則非零值(true);和零(假)否則。

**另請參閱**

* 有關檢查值是否為空,請參閱[是 null()](isnullfunction.md)。
* 有關檢查值是否有限,請參閱[有限()](isfinitefunction.md)。
* 有關檢查值是否為 NaN(不是數位),請參閱[isnan()](isnanfunction.md)。

**範例**

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|NaN|0|
|1|0|∞|1|
