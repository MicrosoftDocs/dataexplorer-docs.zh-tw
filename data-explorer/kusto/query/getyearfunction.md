---
title: 取得年() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 getyear()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e9d4c3e8c793f7775154261febc11e58082132
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514326"
---
# <a name="getyear"></a>getyear()

返回`datetime`參數的年份部分。

**範例**

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```