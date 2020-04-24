---
title: iff() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 iff()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5eb8af5fd98fb24c677390c314d112e5634cacf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514054"
---
# <a name="iff"></a>iff()

計算第一個參數(謂詞),並返回第二個或第三個參數的值,具體取決於謂詞是計算為`true`(第二個)`false`還是 (第三個)。

第二個引數和第三個引數的類型必須相同。

**語法**

`iff(`*謂詞*`,`*(如果True*`,`*如果假)*`)`

**引數**

* *謂詞*:計算到值`boolean`的 運算式。
* *ifTrue*: 如果*謂詞*計算到`true`, 則從 函數中取得計算及其值的運算式。
* *ifFalse*: 如果*謂詞*計算到`false`, 則從 函數中獲取計算及其值的運算式。

**傳回**

如果 predicate** 評估為 `true`，此函式會傳回 ifTrue** 的值，否則會傳回 ifFalse** 的值。

**範例**

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```