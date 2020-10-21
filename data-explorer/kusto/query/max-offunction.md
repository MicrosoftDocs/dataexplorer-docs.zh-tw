---
title: 'max_of ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 max_of。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f2a9cbb64ce1bbbc7d58b66c260d7968d8a60748
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248961"
---
# <a name="max_of"></a>max_of()

傳回數個評估數值運算式的最大值。

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>語法

`max_of``(` *expr_1* `,` *expr_2* .。。`)`

## <a name="arguments"></a>引數

* *expr_i*：要評估的純量運算式。

- 所有引數都必須是相同的類型。
- 最多可支援64個引數。

## <a name="returns"></a>傳回

所有引數運算式的最大值。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
