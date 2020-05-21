---
title: dayofweek （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 dayofweek （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4e445a86b976f251de2beef4726c4840bcec8e44
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722026"
---
# <a name="dayofweek"></a>dayofweek()

傳回從上個星期日算起的整數天數 `timespan` 。

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
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
