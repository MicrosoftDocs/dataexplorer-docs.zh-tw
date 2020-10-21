---
title: 'dayofmonth ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 dayofmonth ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e98e1405a6521ef9cdaf40a24b2c173bdf72c376
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252409"
---
# <a name="dayofmonth"></a>dayofmonth()

傳回代表指定月份之日數的整數數位。

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>語法

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`day number` 在指定月份。