---
title: 周日() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的周()日。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31d3f525653f6e0979229e4355cdec6cb76833f8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516298"
---
# <a name="dayofweek"></a>dayofweek()

將自上一個星期日以來的整數天數傳回`timespan`為 。

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

**語法**

`dayofweek(`*a_date*`)`

**引數**

* `a_date`：`datetime`。

**傳回**

上個星期日開始時之午夜起算的 `timespan` ，捨入為整數天數。

**範例**

```kusto
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```