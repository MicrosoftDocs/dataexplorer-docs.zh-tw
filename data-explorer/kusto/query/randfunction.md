---
title: 蘭特() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 rand()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3638d4b979b7318f58efec0bed0da4c31896a9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510637"
---
# <a name="rand"></a>rand()

返回隨機數。

```kusto
rand()
rand(1000)
```

**語法**

* `rand()`- 返回在`real`範圍 [0.0, 1.0) 中具有均勻分佈的類型值。
* `rand(`*N* `)` - 傳`real`回從集 {0.0、1.0、...、N - 1}*N*中選擇具有統一分佈的類型值。