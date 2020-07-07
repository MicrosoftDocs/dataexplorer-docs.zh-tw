---
title: toint （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 toint （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1efd06b8fa2961528be65b630933aae74614e9a0
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013600"
---
# <a name="toint"></a>toint()

將輸入轉換成整數（帶正負號的32位）數位表示。

```kusto
toint("123") == int(123)
```

**語法**

`toint(`*Expr*`)`

**引數**

* *Expr*：將轉換成整數的運算式。 

**傳回**

如果轉換成功，則結果會是整數。
如果轉換不成功，則結果會是 `null` 。
 
*注意*：可能的話，偏好使用[int （）](./scalar-data-types/int.md) 。
