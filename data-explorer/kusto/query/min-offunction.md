---
title: 'min_of ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 min_of。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c08ef6f147640330cd5ea33c55dcc6acafd77a31
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248823"
---
# <a name="min_of"></a>min_of()

傳回數個評估數值運算式的最小值。

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>語法

`min_of``(` *expr_1* `,` *expr_2* .。。`)`

## <a name="arguments"></a>引數

* *expr_i*：要評估的純量運算式。

- 所有引數都必須是相同的類型。
- 最多可支援64個引數。

## <a name="returns"></a>傳回

所有引數運算式的最小值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
