---
title: 'series_moving_avg_udf ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 使用者定義函數 series_moving_avg_udf。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 526aa1c505dafa681665cccb0c8a8e1d56f80def
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557830"
---
# <a name="series_moving_avg_udf"></a>series_moving_avg_udf ( # A1

在數列上套用移動平均篩選。

函數 `series_moving_avg_udf()` 會採用包含動態數值陣列作為輸入的運算式，並套用 [簡單的移動平均](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) 篩選。

> [!NOTE]
> 此函式是 [UDF (使用者定義函數) ](../query/functions/user-defined-functions.md)。如需詳細資訊，請參閱 [使用](#usage)方式。

## <a name="syntax"></a>語法

`series_moving_avg_udf(`*y_series* `,`*n* `, [`*中心*`])`
  
## <a name="arguments"></a>引數

* *y_series*：數值的動態陣列資料格。
* *n*：移動平均篩選的寬度。
* *置*中：選擇性的布林值，指出移動平均是否為下列其中一個選項：
    * 在目前點前後的視窗上對稱套用，或 
    * 從目前點到後的視窗套用。 <br>
    依預設， *center* 為 False。

## <a name="usage"></a>使用方式

* `series_moving_avg_udf()` 這是使用者定義函數。 您可以在查詢中內嵌其程式碼，或將其安裝在您的資料庫中：
    * 針對臨機操作使用方式，請使用 [let 語句](../query/letstatement.md)內嵌其程式碼。 不需要任何許可權。
    * 若為重複使用，請使用 [. create function](../management/create-function.md)來保存函數。 <br>
        建立函數需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

# <a name="ad-hoc-usage"></a>[特定使用方式](#tab/adhoc)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_udf = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

# <a name="persistent-usage"></a>[持續使用](#tab/persistent)

* **一次安裝**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_udf(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

* **使用量**
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_udf(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-udf/moving-average-five-bins.png" alt-text="描繪5個 bin 移動平均的圖表" border="false":::
