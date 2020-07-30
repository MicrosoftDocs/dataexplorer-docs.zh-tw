---
title: array_iif （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 array_iif （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 1844d87dffb5ac0046c3f62600b8c4914dc93a89
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349579"
---
# <a name="array_iif"></a>array_iif()

動態陣列上的元素型 iif 函數。

另一個別名： array_iff （）。

## <a name="syntax"></a>語法

`array_iif(`*ConditionArray*、 *IfTrue*、 *IfFalse*]`)`

## <a name="arguments"></a>引數

* *conditionArray*：*布林*值或數值的輸入陣列必須是動態陣列。
* *ifTrue*：值或基本值的輸入陣列- *ConditionArray*的對應值為*true*時的結果值。
* *ifFalse*：值或基本值的輸入陣列- *ConditionArray*的對應值為*false*時的結果值。

**備註**

* 結果長度是*conditionArray*的長度。
* 數值條件值會視為*condition* ！ = *0*。
* 非數值/null 條件值在結果的對應索引中會有 null。
* 遺漏值（長度較短的陣列）會視為 null。

## <a name="returns"></a>傳回

根據條件陣列的對應值，從*IfTrue*或*IfFalse* [陣列] 值中取得之值的動態陣列。

## <a name="example"></a>範例

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition (條件)|l|r|res|
|---|---|---|---|
|[true，false，true]|[1, 2, 3]|[4，5，6]|[1，5，3]|
