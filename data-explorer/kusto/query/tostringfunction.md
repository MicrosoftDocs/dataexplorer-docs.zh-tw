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
ms.openlocfilehash: 634f54533e83575139d8399124cc068af56d8574
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741662"
---
# <a name="tostring"></a>tostring()

將輸入轉換為字串表示。

```kusto
tostring(123) == "123"
```

**語法**

`tostring(`*`Expr`*`)`

**引數**

* *`Expr`*：將轉換成字串的運算式。 

**傳回**

如果此*`Expr`* 值為非 null，則結果會是的*`Expr`* 字串表示。
如果*`Expr`* 值為 null，則結果會是空字串。
 