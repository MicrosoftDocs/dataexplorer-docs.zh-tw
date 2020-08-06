---
title: 'tolong ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 tolong ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 36c0317f046f146d2812b8830d7fe571d5363c59
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804112"
---
# <a name="tolong"></a>tolong()

將輸入轉換成 long (帶正負號的64位) 數位表示。

```kusto
tolong("123") == 123
```

> [!NOTE]
> 偏好盡可能使用[long ( # B1](./scalar-data-types/long.md) 。

## <a name="syntax"></a>語法

`tolong(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成 long 的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，則結果會是長數位。
如果轉換不成功，則結果會是 `null` 。
 