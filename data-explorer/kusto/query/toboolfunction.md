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
ms.openlocfilehash: e0343ae5cb98e1cb3114e24c963fe2981be82c5b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350786"
---
# <a name="tobool"></a>tobool()

將輸入轉換成布林值（帶正負號的8位）標記法。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>語法

`tobool(`*Expr* `)` 
 Expr `toboolean(`*Expr* `)`鋸齒

## <a name="arguments"></a>引數

* *Expr*：將轉換成布林值的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，則結果會是布林值。
如果轉換不成功，則結果會是 `null` 。
