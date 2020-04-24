---
title: 每月一天 () - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的月份(天)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 791d85d4a8f89487e65ef68ecc605f907e63e1ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516332"
---
# <a name="dayofmonth"></a>dayofmonth()

傳回表示給定月份天數的整數數字

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

**語法**

`dayofmonth(`*a_date*`)`

**引數**

* `a_date`：`datetime`。

**傳回**

`day number`給定月份。