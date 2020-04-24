---
title: binary_shift_left() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的binary_shift_left()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 15e2bee789e627709ccfedde8eccead7f2578b51
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517556"
---
# <a name="binary_shift_left"></a>binary_shift_left()

返回對一對數位的二進位移位左移操作。

```kusto
binary_shift_left(x,y)  
```

**語法**

`binary_shift_left(`*num1* `,` *num2*`)`

**引數**

* *num1*, *num2*: int 數位。

**傳回**

返回一對數位上的二進位移位左移操作:num1 << (num2%64)。
如果 n 為負,則傳回 NULL 值。