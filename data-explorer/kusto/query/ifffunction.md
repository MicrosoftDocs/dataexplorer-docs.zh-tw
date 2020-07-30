---
title: iff （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 iff （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7eeab87f3c3ef42d1e00bf0d6b8853fe3a2f3125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347488"
---
# <a name="iff"></a>iff()

評估第一個引數（述詞），並傳回第二個或第三個引數的值，視述詞是否評估為 `true` （秒）或 `false` （第三個）而定。

第二個引數和第三個引數的類型必須相同。

## <a name="syntax"></a>語法

`iff(`述詞*predicate* `,`*ifTrue* `,`*ifFalse*`)`

## <a name="arguments"></a>引數

* 述*詞：評估*為值的運算式 `boolean` 。
* *ifTrue*：如果述詞評估為 *，則會*評估並從函式傳回其值的運算式 `true` 。
* *ifFalse*：如果述詞評估為 *，則會*評估並從函式傳回其值的運算式 `false` 。

## <a name="returns"></a>傳回

如果 predicate** 評估為 `true`，此函式會傳回 ifTrue** 的值，否則會傳回 ifFalse** 的值。

## <a name="example"></a>範例

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```