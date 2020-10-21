---
title: 'toguid ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 toguid ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d122833f4797c8503dd41cc8ba861554d6924338
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250381"
---
# <a name="toguid"></a>toguid()

將輸入轉換為 [`guid`](./scalar-data-types/guid.md) 表示。

```kusto
toguid("70fc66f7-8279-44fc-9092-d364d70fce44") == guid("70fc66f7-8279-44fc-9092-d364d70fce44")
```

> [!NOTE]
> 最好盡可能使用 [guid ( # B1 ](./scalar-data-types/guid.md) 。

## <a name="syntax"></a>語法

`toguid(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成純量的運算式 [`guid`](./scalar-data-types/guid.md) 。 

## <a name="returns"></a>傳回

如果轉換成功，結果將會是純量 [`guid`](./scalar-data-types/guid.md) 。
如果轉換不成功，結果將會是 `null` 。
