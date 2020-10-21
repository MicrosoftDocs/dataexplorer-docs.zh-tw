---
title: '如果 ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的如果 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5cc6a41c8b74e4fd08eebbe968b7384dce39039e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252280"
---
# <a name="iff"></a>iff()

評估 (述詞) 的第一個引數，並傳回第二個或第三個引數的值，取決於述詞是否評估為 `true` (第二個) 或 `false` (第三個) 。

第二個引數和第三個引數的類型必須相同。

## <a name="syntax"></a>語法

`iff(`述詞*predicate* `,`*ifTrue* `,`*ifFalse*`)`

## <a name="arguments"></a>引數

* 述*詞：評估*為值的運算式 `boolean` 。
* *ifTrue* *：當述* 詞評估為時，會取得評估的運算式以及從函式傳回的值 `true` 。
* *ifFalse* *：當述* 詞評估為時，會取得評估的運算式以及從函式傳回的值 `false` 。

## <a name="returns"></a>傳回

如果 predicate** 評估為 `true`，此函式會傳回 ifTrue** 的值，否則會傳回 ifFalse** 的值。

## <a name="example"></a>範例

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```