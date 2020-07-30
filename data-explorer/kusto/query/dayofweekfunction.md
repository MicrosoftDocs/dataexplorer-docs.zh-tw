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
ms.openlocfilehash: 04b6122c7517d79d5563892a621eed8cde3b948a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348474"
---
# <a name="dayofweek"></a>dayofweek()

傳回從上個星期日算起的整數天數 `timespan` 。

```kusto
dayofweek(datetime(2015-12-14)) == 1d  // Monday
```

## <a name="syntax"></a>語法

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

上個星期日開始時之午夜起算的 `timespan` ，捨入為整數天數。

## <a name="examples"></a>範例

```kusto
dayofweek(datetime(1947-11-30 10:00:05))  // time(0.00:00:00), indicating Sunday
dayofweek(datetime(1970-05-11))           // time(1.00:00:00), indicating Monday
```
