---
title: 月份() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的月份。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ada34d3e6f6c905a1acfb550b02af3dab550fb8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512337"
---
# <a name="monthofyear"></a>monthofyear()

返回整數表示給定年份的月份編號。

另一個別名:獲取月()

```kusto
monthofyear(datetime("2015-12-14"))
```

**語法**

`monthofyear(`*a_date*`)`

**引數**

* `a_date`：`datetime`。

**傳回**

`month number`給定的年份。