---
title: 'totimespan ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 totimespan ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0779a6260cc87f8a602f4751d28c33de9bacb57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243746"
---
# <a name="totimespan"></a>totimespan()

將輸入轉換成 [timespan](./scalar-data-types/timespan.md) 純量。

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>語法

`totimespan(Expr)`

## <a name="arguments"></a>引數

* *`Expr`*：將轉換成 [timespan](./scalar-data-types/timespan.md)的運算式。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是 [timespan](./scalar-data-types/timespan.md) 值。
否則，結果會是 null。
