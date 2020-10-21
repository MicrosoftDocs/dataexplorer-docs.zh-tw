---
title: 'isfinite ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isfinite ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b428d43afd7984bbcf19351da702517a3a1244a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242311"
---
# <a name="isfinite"></a>isfinite()

傳回輸入是否為有限值 (不是無限值也不是 NaN) 。

## <a name="syntax"></a>語法

`isfinite(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

如果 x 為有限的，則非零值 (true) 。和零 (false) 否則為。

## <a name="see-also"></a>另請參閱

* 若要檢查值是否為 null，請參閱 [isnull ( # B1 ](isnullfunction.md)。
* 若要檢查值是否為無限，請參閱 [isinf ( # B1 ](isinffunction.md)。
* 若要檢查值是否為 NaN (非數位) ，請參閱 [isnan ( # B3 ](isnanfunction.md)。

## <a name="example"></a>範例

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isfinite=isfinite(div)
```

|x|y|div|isfinite|
|---|---|---|---|
|-1|0|-∞|0|
|0|0|NaN|0|
|1|0|∞|0|