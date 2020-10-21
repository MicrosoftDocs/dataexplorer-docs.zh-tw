---
title: 'tolong ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 tolong ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bf4960ae3bd11697e4e7203438e1a33af6c90672
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241004"
---
# <a name="tolong"></a>tolong()

將輸入轉換為 long (帶正負號的64位) 數位標記法。

```kusto
tolong("123") == 123
```

> [!NOTE]
> 最好盡可能使用 [long ( # B1 ](./scalar-data-types/long.md) 。

## <a name="syntax"></a>語法

`tolong(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換為 long 的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，結果將會是較長的數位。
如果轉換不成功，結果將會是 `null` 。
 