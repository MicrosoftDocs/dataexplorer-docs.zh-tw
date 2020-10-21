---
title: 'array_iif ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 array_iif。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: 7f11e0c00b19fd24ed2db326f3209236ecf25ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246893"
---
# <a name="array_iif"></a>array_iif()

動態陣列上的元素型 iif 函數。

另一個別名： array_iff ( # A1。

## <a name="syntax"></a>語法

`array_iif(`*ConditionArray*、 *IfTrue*、 *IfFalse*]`)`

## <a name="arguments"></a>引數

* *conditionArray*： *布林* 值或數值的輸入陣列必須是動態陣列。
* *ifTrue*：值或基本值的輸入陣列-當 *ConditionArray* 的對應值為 *true*時，結果值 (s) 。
* *ifFalse*：值或基本值的輸入陣列-當 *ConditionArray* 的對應值為 *false*時，結果值 (s) 。

**備註**

* 結果長度是 *conditionArray*的長度。
* 數值條件值會被視為 *condition* ！ = *0*。
* 在結果的對應索引中，非數值/null 條件值會有 null。
*  (在較短長度陣列中的遺漏值，) 會被視為 null。

## <a name="returns"></a>傳回

根據條件陣列的對應值，從 *IfTrue* 或 *IfFalse* [array] 值取得值的動態陣列。

## <a name="example"></a>範例

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition (條件)|l|r|res|
|---|---|---|---|
|[true，false，true]|[1, 2, 3]|[4，5，6]|[1，5，3]|
