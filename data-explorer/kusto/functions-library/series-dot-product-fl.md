---
title: 'series_dot_product_fl ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 使用者定義函數 series_dot_product_fl。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 10/18/2020
ms.openlocfilehash: 9065c6b86807ad27c588ebffe3334c450a1addcb
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321840"
---
# <a name="series_dot_product_fl"></a>series_dot_product_fl()

計算兩個數值向量的點乘積。

函數 `series_dot_product_fl()` 會採用包含兩個動態數值陣列作為輸入的運算式，並計算其 [點乘積](https://en.wikipedia.org/wiki/Dot_product)。

> [!NOTE]
> 此函式是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。 如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`series_dot_product_fl(`*vec1* `,`*vec2*`)`
  
## <a name="arguments"></a>引數

* *vec1*：數值的動態陣列資料格。
* *vec2*：數值的動態陣列資料格，與 *vec1* 的長度相同。

## <a name="usage"></a>使用方式

`series_dot_product_fl()` 這是使用者定義函數。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中。 有兩種使用方式選項：臨機操作和持續使用。 如需範例，請參閱下列索引標籤。

# <a name="ad-hoc"></a>[臨機操作](#tab/adhoc)

針對臨機操作使用方式，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_dot_product_fl=(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
};
//
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

# <a name="persistent"></a>[持續](#tab/persistent)

若要持續使用，請使用 [`.create function`](../management/create-function.md) 。 建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

### <a name="one-time-installation"></a>一次安裝

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate the dot product of 2 numerical arrays")
series_dot_product_fl(vec1:dynamic, vec2:dynamic)
{
    let elem_prod = series_multiply(vec1, vec2);
    let cum_sum = series_iir(elem_prod, dynamic([1]), dynamic([1,-1]));
    todouble(cum_sum[-1])
}
```

### <a name="usage"></a>使用方式

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
union
(print 1 | project v1=range(1, 3, 1), v2=range(4, 6, 1)),
(print 1 | project v1=range(11, 13, 1), v2=range(14, 16, 1))
| extend v3=series_dot_product_fl(v1, v2)
```

---

:::image type="content" source="images/series-dot-product-fl/dot-product-result.png" alt-text="此表格顯示在 Azure 中使用使用者定義函數 series_dot_product_fl 的2個向量的點乘積結果資料總管" border="false":::
