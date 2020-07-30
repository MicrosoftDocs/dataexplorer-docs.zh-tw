---
title: iif （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 iif （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d912f94a224b073fe9214f70077067d3a24c906
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347471"
---
# <a name="iif"></a>iif()

評估第一個引數（述詞），並傳回第二個或第三個引數的值，視述詞是否評估為 `true` （秒）或 `false` （第三個）而定。

第二個引數和第三個引數的類型必須相同。

## <a name="syntax"></a>語法

`iif(`述詞*predicate* `,`*ifTrue* `,`*ifFalse*`)`

## <a name="arguments"></a>引數

* 述*詞：評估*為值的運算式 `boolean` 。
* *ifTrue*：如果述詞評估為 *，則會*評估並從函式傳回其值的運算式 `true` 。
* *ifFalse*：如果述詞評估為 *，則會*評估並從函式傳回其值的運算式 `false` 。

## <a name="returns"></a>傳回

如果 predicate** 評估為 `true`，此函式會傳回 ifTrue** 的值，否則會傳回 ifFalse** 的值。

## <a name="example"></a>範例

```kusto
T 
| extend day = iif(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

的別名 [`iff()`](ifffunction.md) 。