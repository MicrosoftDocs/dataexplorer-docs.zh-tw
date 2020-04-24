---
title: binary_xor() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的binary_xor()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2f487aa44f8885cbb443c31b8bb3a503e1a76fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517522"
---
# <a name="binary_xor"></a>binary_xor()

返回兩個值的位操作`xor`的結果。

```kusto
binary_xor(x,y)
```

**語法**

`binary_xor(`*num1* `,` *num2*`)`

**引數**

* *數位1,**數位2:* 長數。

**傳回**

返回對一對數位的邏輯 XOR 操作:num1 = num2。