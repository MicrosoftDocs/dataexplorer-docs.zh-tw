---
title: binary_and() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 binary_and()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c5501218c1ddb69a73f685fda78f3b3482afb5c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517675"
---
# <a name="binary_and"></a>binary_and()

返回兩個值之間的位操作`and`的結果。

```kusto
binary_and(x,y) 
```

**語法**

`binary_and(`*num1* `,` *num2*`)`

**引數**

* *數位1,**數位2:* 長數。

**傳回**

返回對一對數位的邏輯 AND 操作:num1 & num2。