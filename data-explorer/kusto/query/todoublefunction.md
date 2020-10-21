---
title: 'todouble ( # B1/toreal ( # A3-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 todouble ( # B1/toreal ( # A3。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a1a18ffdfc28d0487464202baa759acccd3e40e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250473"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

將輸入轉換為類型的值 `real` 。  (`todouble()` 和 `toreal()` 都是同義字。 ) 

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> 偏好盡可能使用 [雙 ( # A1 或實數 ( # B3 ](./scalar-data-types/real.md) 。

## <a name="syntax"></a>語法

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

## <a name="arguments"></a>引數

* *運算式，* 其值會轉換成類型的值 `real` 。

## <a name="returns"></a>傳回

如果轉換成功，則結果為類型的值 `real` 。
如果轉換不成功，則結果為值 `real(null)` 。
