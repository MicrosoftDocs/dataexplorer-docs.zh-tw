---
title: binary_shift_right() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的binary_shift_right()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94c8c695f1c5d16ee0a7e3a92882486b8a8ef5d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517539"
---
# <a name="binary_shift_right"></a>binary_shift_right()

返回對一對數位的二進位移位右操作。

```kusto
binary_shift_right(x,y) 
```

**語法**

`binary_shift_right(`*num1* `,` *num2*`)`

**引數**

* *數位1,**數位2:* 長數。

**傳回**

返回對一對數位的二進位移位右操作:num1 >> (num2%64)。
如果 n 為負,則傳回 NULL 值。