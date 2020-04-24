---
title: 到時間跨度() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的到時間跨度()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 504d4a74e8c1b58a8a97fd80d6c846fcf7e3f527
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505860"
---
# <a name="totimespan"></a>totimespan()

將輸入轉換為[時間跨度](./scalar-data-types/timespan.md)標量。

```kusto
totimespan("0.00:01:00") == time(1min)
```

**語法**

`totimespan(`*Expr*`)`

**引數**

* *Expr*: 將轉換為[時間跨度](./scalar-data-types/timespan.md)的運算式 。 

**傳回**

如果轉換成功,結果將是[一個時間跨度](./scalar-data-types/timespan.md)值。
如果轉換不成功,結果將為空。
 