---
title: 一天() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的"年(")日。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41e7c5906da001e877dd9124124e126d729e886d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516213"
---
# <a name="dayofyear"></a>dayofyear()

返回整數表示給定年份的天號。

```kusto
dayofyear(datetime(2015-12-14))
```

**語法**

`dayofweek(`*a_date*`)`

**引數**

* `a_date`：`datetime`。

**傳回**

`day number`給定的年份。