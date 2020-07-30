---
title: max_of （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 max_of （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2045436de09bc31fa0378824310fa872478b861
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346825"
---
# <a name="max_of"></a>max_of()

傳回數個已評估之數值運算式的最大值。

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>語法

`max_of``(` *expr_1* `,` *expr_2* .。。`)`

## <a name="arguments"></a>引數

* *expr_i*：要評估的純量運算式。

- 所有引數都必須是相同的類型。
- 最多支援64個引數。

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
