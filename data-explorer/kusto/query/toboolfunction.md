---
title: tobool （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 tobool （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f99406d94e1cd64da8605e5000aa99136c2b119a
ms.sourcegitcommit: e093e4fdc7dafff6997ee5541e79fa9db446ecaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85763793"
---
# <a name="tobool"></a>tobool()

將輸入轉換成布林值（帶正負號的8位）標記法。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**語法**

`tobool(`*Expr* `)` 
 Expr `toboolean(`*Expr* `)`鋸齒

**引數**

* *Expr*：將轉換成布林值的運算式。 

**傳回**

如果轉換成功，則結果會是布林值。
如果轉換不成功，則結果會是 `null` 。
