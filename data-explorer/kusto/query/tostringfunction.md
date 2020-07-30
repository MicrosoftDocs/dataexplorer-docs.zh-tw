---
title: tostring （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 tostring （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2093ff1117cf7744af550cf93c3fe630fa40a6e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340169"
---
# <a name="tostring"></a>tostring()

將輸入轉換為字串表示。

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>語法

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>引數

* *`Expr`*：將轉換成字串的運算式。 

## <a name="returns"></a>傳回

如果此 *`Expr`* 值為非 null，則結果會是的字串表示 *`Expr`* 。
如果 *`Expr`* 值為 null，則結果會是空字串。
 