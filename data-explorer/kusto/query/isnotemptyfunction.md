---
title: 非空() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的非空()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 14111be0fc0247dd151ef7454121e6b90a32ff0d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513527"
---
# <a name="isnotempty"></a>isnotempty()

如果`true`參數不是空字串,也不是空字串,則返回。

```kusto
isnotempty("") == false
```

**語法**

`isnotempty(`[*value*]`)`

`notempty(`[*value*]`)` -- 別名`isnotempty`