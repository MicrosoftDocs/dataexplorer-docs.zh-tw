---
title: 'dayofyear ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 dayofyear ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2ac301365cb22849dc07ea137756f4093bea79b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252394"
---
# <a name="dayofyear"></a>dayofyear()

傳回整數值，代表指定年份的日數。

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>語法

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`day number` 在指定年份中。