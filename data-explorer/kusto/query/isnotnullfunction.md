---
title: isnotnull() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 isnotnull()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8be57f2f7484081ac316a8af6fea252a60f895a4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513442"
---
# <a name="isnotnull"></a>不無()

如果`true`參數不為空,則返回。

**語法**

`isnotnull(`[*value*]`)`

`notnull(`[*value*]`)` - 別名`isnotnull`

**範例**

```kusto
T | where isnotnull(PossiblyNull) | count
```

請注意，還有其他方式能達成此效果︰

```kusto
T | summarize count(PossiblyNull)
```