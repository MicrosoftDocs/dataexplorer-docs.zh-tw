---
title: 上限（）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的上限（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e2a29d28b25d26d582aa49717d5ce5576276f450
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348899"
---
# <a name="ceiling"></a>ceiling()

計算大於或等於指定之數值運算式的最小整數。

## <a name="syntax"></a>語法

`ceiling(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

* 大於或等於指定之數值運算式的最小整數。 

## <a name="examples"></a>範例

```kusto
print c1 = ceiling(-1.1), c2 = ceiling(0), c3 = ceiling(0.9)
```

|c1|c2|c3|
|---|---|---|
|-1|0|1|