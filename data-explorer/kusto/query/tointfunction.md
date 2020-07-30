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
ms.openlocfilehash: 2daea4d190ed349c41a8eecf2eef53b2c2b93716
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350684"
---
# <a name="toint"></a>toint()

將輸入轉換成整數（帶正負號的32位）數位表示。

```kusto
toint("123") == int(123)
```

## <a name="syntax"></a>語法

`toint(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成整數的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，則結果會是整數。
如果轉換不成功，則結果會是 `null` 。
 
*注意*：可能的話，偏好使用[int （）](./scalar-data-types/int.md) 。
