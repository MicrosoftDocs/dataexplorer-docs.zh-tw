---
title: isnan （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 isnan （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5597f21d5e426329e2793978a6b207efc3868d13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347216"
---
# <a name="isnan"></a>isnan()

傳回輸入是否為非數位（NaN）值。  

## <a name="syntax"></a>語法

`isnan(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

如果 x 是 NaN，則為非零值（true）;否則為零（false）。

**另請參閱**

* 若要檢查值是否為 null，請參閱[isnull （）](isnullfunction.md)。
* 若要檢查值是否為有限，請參閱[isfinite （）](isfinitefunction.md)。
* 若要檢查值是否為無限，請參閱[isinf （）](isinffunction.md)。

## <a name="example"></a>範例

```kusto
range x from -1 to 1 step 1
| extend y = (-1*x) 
| extend div = 1.0*x/y
| extend isnan=isnan(div)
```

|x|y|div|isnan|
|---|---|---|---|
|-1|1|-1|0|
|0|0|NaN|1|
|1|-1|-1|0|