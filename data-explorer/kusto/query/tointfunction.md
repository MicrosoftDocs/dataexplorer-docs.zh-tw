---
title: toint （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 toint （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7f0ede908be2689165f641038b2b6f699c0eb543
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550600"
---
# <a name="toint"></a>toint()

將輸入轉換成整數（帶正負號的32位）數位表示。

```kusto
toint("123") == 123s
```

**語法**

`toint(`*Expr*`)`

**引數**

* *Expr*：將轉換成整數的運算式。 

**傳回**

如果轉換成功，則結果會是整數。
如果轉換不成功，則結果會是 `null` 。
 
*注意*：可能的話，偏好使用[int （）](./scalar-data-types/int.md) 。