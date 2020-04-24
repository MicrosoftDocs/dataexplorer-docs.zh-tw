---
title: binary_or() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的binary_or()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f6d68137ded3b164ca25d82e0c17b6fef6fc1a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517590"
---
# <a name="binary_or"></a>binary_or()

返回兩個值的位操作`or`的結果。 

```kusto
binary_or(x,y)
```

**語法**

`binary_or(`*num1* `,` *num2*`)`

**引數**

* *數位1,**數位2:* 長數。

**傳回**

返回對一對數字的邏輯 OR 操作:num1 |num2.