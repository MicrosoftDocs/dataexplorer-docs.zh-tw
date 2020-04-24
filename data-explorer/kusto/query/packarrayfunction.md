---
title: pack_array() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的pack_array()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad13efd6b0743a3c092b2859ea032c3731ffdf1b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511861"
---
# <a name="pack_array"></a>pack_array()

將所有輸入值打包到動態陣列中。

**語法**

`pack_array(`*Expr1*`[`` *Expr2*]`, )

**引數**

* *Expr1...N*: 要打包到動態數位的輸入運算式。

**傳回**

動態陣列,包括 Expr1、Expr2、...、 ExprN 的值。

**範例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1,2,4]|
|[2,4,8]|
|[3,6,12]|

```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Column1|
|---|
|[1,"2","00:00:02"]|
|[2,"4","00:00:04"]|
|[3,"6","00:00:06"]|