---
title: 'tostring ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 tostring ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 43797dcaa5e1a18b6a84f15fc0af89d88d814ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243805"
---
# <a name="tostring"></a>tostring()

將輸入轉換成字串表示。

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>語法

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>引數

* *`Expr`*：將轉換成字串的運算式。 

## <a name="returns"></a>傳回

如果此 *`Expr`* 值為非 null，則結果將會是的字串表示 *`Expr`* 。
如果 *`Expr`* 值為 null，則結果會是空字串。
 