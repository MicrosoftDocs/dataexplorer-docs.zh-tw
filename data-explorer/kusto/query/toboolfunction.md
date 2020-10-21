---
title: 'tobool ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 tobool ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e7fa3d12b1dfc1fcbede2f5f47b3841102b72e5a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250579"
---
# <a name="tobool"></a>tobool()

將輸入轉換為布林值， (帶正負號的8位) 標記法。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

## <a name="syntax"></a>語法

`tobool(`*Expr* `)` 
 Expr `toboolean(`*Expr* `)` (別名) 

## <a name="arguments"></a>引數

* *Expr*：將轉換成布林值的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，結果將會是布林值。
如果轉換不成功，結果將會是 `null` 。
