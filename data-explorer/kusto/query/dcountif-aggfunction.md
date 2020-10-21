---
title: 'dcountif ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 dcountif ( # A1 (彙總函式) 。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c14c4d9e0512d4bb054bb3df39acb4828be5a5bd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247541"
---
# <a name="dcountif-aggregation-function"></a>dcountif ( # A1 (彙總函式) 

傳回述詞評估*為之資料*列的*運算式*的相異值數目的估計值 `true` 。 

* 只能用在 [摘要內匯總](summarizeoperator.md)的內容中。

瞭解 [估計精確度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>語法

摘要 `dcountif(` *Expr*、 *Predicate*述詞、[ `,` *精確度*]`)`

## <a name="arguments"></a>引數

* *Expr*：將用於匯總計算的運算式。
* 述*詞：將*用來篩選資料列的運算式。
* ** (若已指定) 會控制速度和精確度之間的平衡。
    * `0` = 最不精確但最快速的計算。 1.6% 錯誤
    * `1` = 預設值，可平衡精確度和計算時間;大約0.8% 錯誤。
    * `2` = 精確和緩慢的計算;大約0.4% 錯誤。
    * `3` = 額外的精確和緩慢的計算;大約0.28% 錯誤。
    * `4` = 超級精確且最慢的計算;大約0.2% 錯誤。
    
## <a name="returns"></a>傳回

傳回在群組中，述*詞評估為*之資料列之*運算式*的相異值數目的估計值 `true` 。 

## <a name="example"></a>範例

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**秘訣：位移誤差**

`dcountif()`在所有資料列都通過的邊緣案例中，或沒有任何資料列通過（運算式）時，可能會產生一次性錯誤 `Predicate`