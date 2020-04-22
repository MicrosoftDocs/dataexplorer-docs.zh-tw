---
title: array_iif() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的array_iif()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/28/2019
ms.openlocfilehash: f99b9aa8a9d081a7f28cd2e5bb8750b15f2fcdac
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663907"
---
# <a name="array_iif"></a>array_iif()

動態陣列上的元素 iif 函數。

另一個別名:array_iff()。

**語法**

`array_iif(`*條件陣列*,*如果 True*,*如果假*-`)`

**引數**

* *條件陣列*:布*林 值*或數值的輸入陣列,必須是動態數位。
* *ifTrue*:輸入值或基元值的陣列 -*當條件陣列*的相應值*為 true*時的結果值。
* *ifFalse*: 輸入值或基元值的陣列 -*當條件陣列*的相應值*為 false*時的結果值。

**注意事項**

* 結果長度是*條件陣組*的長度。
* 數值準則的數字值被*視為條件*。= *0*。
* 非數位/空條件值在結果的相應索引中將為空。
* 缺少的值(在較短長度的陣列中)被視為空值。

**傳回**

根據條件陣列的相應值從*IfTrue*或*IfFalse* [陣列] 值獲取的值的動態陣列。

**範例**

```kusto
print condition=dynamic([true,false,true]), l=dynamic([1,2,3]), r=dynamic([4,5,6]) 
| extend res=array_iif(condition, l, r)
```

|condition (條件)|l|r|res|
|---|---|---|---|
|[真,假,真]|[1, 2, 3]|[4, 5, 6]|[1, 5, 3]|
