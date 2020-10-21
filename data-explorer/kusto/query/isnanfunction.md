---
title: 'isnan ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isnan ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8cde58db626ca7bc3433e8e36c8d369a7e592b1
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253247"
---
# <a name="isnan"></a>isnan()

傳回輸入是否不是數位 (NaN) 值。  

## <a name="syntax"></a>語法

`isnan(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

如果 x 是 NaN，則非零值 (true) 。和零 (false) 否則為。

## <a name="see-also"></a>另請參閱

* 若要檢查值是否為 null，請參閱 [isnull ( # B1 ](isnullfunction.md)。
* 若要檢查值是否為有限，請參閱 [isfinite ( # B1 ](isfinitefunction.md)。
* 若要檢查值是否為無限，請參閱 [isinf ( # B1 ](isinffunction.md)。

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