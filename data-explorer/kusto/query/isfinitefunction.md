---
title: isfinite （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 isfinite （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f53c74fd2ac56219351c4d194c93e9fa4b627cf4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347250"
---
# <a name="isfinite"></a>isfinite()

傳回輸入是否為有限值（既不是無限的，也不是 NaN）。

## <a name="syntax"></a>語法

`isfinite(`*x*`)`

## <a name="arguments"></a>引數

* *x*：實數。

## <a name="returns"></a>傳回

如果 x 是有限的，則為非零值（true）;否則為零（false）。

**另請參閱**

* 若要檢查值是否為 null，請參閱[isnull （）](isnullfunction.md)。
* 若要檢查值是否為無限，請參閱[isinf （）](isinffunction.md)。
* 若要檢查值是否為 NaN （非數位），請參閱[isnan （）](isnanfunction.md)。

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