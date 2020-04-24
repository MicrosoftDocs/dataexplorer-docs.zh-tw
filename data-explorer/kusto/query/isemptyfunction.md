---
title: 是空() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的為空()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cb0e53aa16257398c20661c31494ca9dda17c1e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513612"
---
# <a name="isempty"></a>isempty()

如果`true`參數為空字串或為 null,則返回。
    
```kusto
isempty("") == true
```

**語法**

`isempty(`[*value*]`)`

**傳回**

指出引數為空字串還是 null。

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|解析森(""){}|false

**範例**

```kusto
T
| where isempty(fieldName)
| count
```