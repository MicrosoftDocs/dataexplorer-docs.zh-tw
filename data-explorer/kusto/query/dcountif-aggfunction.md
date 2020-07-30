---
title: dcountif （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 dcountif （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac07b51135c202f611ba28931eebf79ef5e996f1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348389"
---
# <a name="dcountif-aggregation-function"></a>dcountif （）（彙總函式）

傳回述*詞評估為的資料*列*Expr*的相異值數目估計 `true` 。 

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中。

閱讀[估計精確度](dcount-aggfunction.md#estimation-accuracy)的相關資訊。

## <a name="syntax"></a>語法

總結 `dcountif(` *Expr*、 *Predicate*述詞、[ `,` *精確度*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：將*用來篩選資料列的運算式。
* ** (若已指定) 會控制速度和精確度之間的平衡。
    * `0` = 最不精確但最快速的計算。 1.6% 錯誤
    * `1`= 預設值，它會平衡精確度和計算時間;大約0.8% 錯誤。
    * `2`= 精確和緩慢的計算;大約0.4% 錯誤。
    * `3`= 更精確和緩慢的計算;大約0.28% 錯誤。
    * `4`= 超級精確且最慢的計算;大約0.2% 錯誤。
    
## <a name="returns"></a>傳回

傳回在群組中，述詞評估*為之資料*列*Expr*的相異值數目估計 `true` 。 

## <a name="example"></a>範例

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**提示：位移錯誤**

`dcountif()`可能會導致在所有資料列通過的邊緣案例中發生一次性錯誤，或沒有任何資料列通過 `Predicate` 運算式