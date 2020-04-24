---
title: isutf8() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 isutf8()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 619fb90b72fed8ec0e10fe05ddc3c6df6ff1386e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513391"
---
# <a name="isutf8"></a>isutf8()

如果`true`參數是有效的 utf8 字串,則返回。
    
```kusto
isutf8("some string") == true
```

**語法**

`isutf8(`[*value*]`)`

**傳回**

指示參數是否是有效的 utf8 字串。

**範例**

```kusto
T
| where isutf8(fieldName)
| count
```