---
title: todouble （）/toreal （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 todouble （）/toreal （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 34734f3975b1720c1d009f190d4fae2ebc54283f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350752"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

將輸入轉換為類型的值 `real` 。 （ `todouble()` 和 `toreal()` 都是同義字）。

```kusto
toreal("123.4") == 123.4
```

## <a name="syntax"></a>語法

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：其值將轉換成類型值的運算式 `real` 。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是類型的值 `real` 。
如果轉換不成功，則結果會是值 `real(null)` 。

*注意*：偏好使用[double （）或 real （）（](./scalar-data-types/real.md)可能的話）。