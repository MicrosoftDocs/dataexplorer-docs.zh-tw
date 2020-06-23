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
ms.openlocfilehash: e62432773d99d74a46022cad3199f3bab0cae50b
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128445"
---
# <a name="todouble-toreal"></a>todouble （）、toreal （）

將輸入轉換為類型的值 `real` 。 （ `todouble()` 和 `toreal()` 都是同義字）。

```kusto
toreal("123.4") == 123.4
```

**語法**

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

**引數**

* *Expr*：其值將轉換成類型值的運算式 `real` 。

**傳回**

如果轉換成功，則結果會是類型的值 `real` 。
如果轉換不成功，則結果會是值 `real(null)` 。

*注意*：偏好使用[double （）或 real （）（](./scalar-data-types/real.md)可能的話）。