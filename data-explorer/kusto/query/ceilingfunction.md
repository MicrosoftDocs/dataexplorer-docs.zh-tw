---
title: '上限 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的上限 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b307121e102229edbe62c4d4dd7f910ea2ce8756
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249325"
---
# <a name="ceiling"></a>ceiling()

計算大於或等於指定之數值運算式的最小整數。

## <a name="syntax"></a>語法

`ceiling(`*X*`)`

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