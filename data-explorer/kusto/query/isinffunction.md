---
title: 'isinf ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 isinf ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e9fa24aeec2cc70b41e1fcd4f9d1185ef80b714b
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103239"
---
# <a name="isinf"></a>isinf()

傳回輸入是否為無限 (正數或負) 值。  

## <a name="syntax"></a>語法

`isinf(`*X*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

如果 x 是正數或負無限大，則非零值 (true) 。和零 (false) 否則為。

## <a name="see-also"></a>另請參閱

* 若要檢查值是否為 null，請參閱 [isnull ( # B1 ](isnullfunction.md)。
* 若要檢查值是否為有限，請參閱 [isfinite ( # B1 ](isfinitefunction.md)。
* 若要檢查值是否為 NaN (非數位) ，請參閱 [isnan ( # B3 ](isnanfunction.md)。

## <a name="example"></a>範例

```kusto
range x from -1 to 1 step 1
| extend y = 0.0
| extend div = 1.0*x/y
| extend isinf=isinf(div)
```

|x|y|div|isinf|
|---|---|---|---|
|-1|0|-∞|1|
|0|0|NaN|0|
|1|0|∞|1|
