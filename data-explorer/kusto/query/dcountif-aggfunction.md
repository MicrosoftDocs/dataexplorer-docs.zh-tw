---
title: dcountif() (聚合函數) - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 dcountif(聚合函數)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1cc3c17474db835f381cac32d6107acb312c3d11
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516043"
---
# <a name="dcountif-aggregation-function"></a>dcountif() (聚合函數)

返回 *"謂詞*"`true`計算到 的行*Expr*不同值數的估計值。 

* 只能在[匯總](summarizeoperator.md)中的聚合上下文中使用。

閱讀有關[估計準確性](dcount-aggfunction.md#estimation-accuracy)的。

**語法**

總結`dcountif(` *Expr*,`,`*謂詞*, [*準確性*]`)`

**引數**

* *Expr*:將用於聚合計算的運算式。
* *謂詞*:將用於篩選行的運算式。
* ** (若已指定) 會控制速度和精確度之間的平衡。
    * `0` = 最不精確但最快速的計算。 1.6% 錯誤
    * `1`• 預設值,它平衡了準確性和計算時間;約 0.8% 錯誤。
    * `2`• 準確和緩慢的計算;約 0.4% 錯誤。
    * `3`• 超精確和緩慢的計算;約 0.28% 錯誤。
    * `4`• 超精確和最慢的計算;約 0.2% 錯誤。
    
**傳回**

返回 *"謂詞"*`true`在組中計算到的行*Expr*不同值數的估計值。 

**範例**

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**提示:位移錯誤**

`dcountif()`在所有行通過或沒有行通過時,可能會導致一次性錯誤, `Predicate`