---
title: isascii() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 isascii()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: daba0f4015a4847155309964f8ac0909ff4bc9d0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513714"
---
# <a name="isascii"></a>isascii()

如果`true`參數是有效的 ascii 字串,則返回。
    
```kusto
isascii("some string") == true
```

**語法**

`isascii(`[*value*]`)`

**傳回**

指示參數是否是有效的 ascii 字串。

**範例**

```kusto
T
| where isascii(fieldName)
| count
```