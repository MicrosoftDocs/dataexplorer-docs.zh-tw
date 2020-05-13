---
title: render 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的轉譯運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 20e512d55568f39ea21d3ddcb383adaf0fa7dab3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373040"
---
# <a name="render-operator"></a>render 運算子

指示使用者代理程式以特定方式呈現查詢的結果。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * Render 運算子應該是查詢中的最後一個運算子，而且只能搭配產生單一表格式資料流程結果的查詢使用。
> * Render 運算子不會修改資料。 它會將注釋（「視覺效果」）插入結果的擴充屬性中。 批註包含查詢中運算子所提供的資訊。
> * 視覺效果資訊的轉譯是由使用者代理程式所完成。 不同的代理程式（例如 Kusto. Explorer、Kusto. WebExplorer）可能支援不同的視覺效果。

**語法**

*T* `|` `render` *視覺效果*[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

其中：

* *視覺效果*表示要使用的視覺效果類型。 支援的值為：

::: zone pivot="azuredataexplorer"

|*視覺效果*     |描述|
|--------------------|-|
| `anomalychart`     | 類似于 timechart，但使用[series_decompose_anomalies](./series-decompose-anomaliesfunction.md)函數來反白[顯示異常](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)。 |
| `areachart`        | 區域圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 y 軸。 |
| `barchart`         | 第一個資料行是 X 軸，而且可以是文字、日期時間或數值。 其他資料行是數值，會顯示為水準去除。|
| `card`             | 第一個結果記錄會被視為純量值的集合，並顯示為卡片。 |
| `columnchart`      | `barchart`與垂直線相似，而不是水準的去除。|
| `ladderchart`      | 最後兩個數據行是 X 軸，其他資料行則是 y 軸。|
| `linechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 y 軸。 |
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `pivotchart`       | 顯示樞紐分析表和圖表。 使用者可以互動方式選取資料、資料行、資料列和各種圖表類型。 |
| `scatterchart`     | 點圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 y 軸。 |
| `stackedareachart` | 堆疊區域圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 y 軸。 |
| `table`            | 預設值-結果會顯示為數據表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他（數值）資料行是 y 軸。 有一個字串資料行的值是用來「分組」數值欄位，並在圖表中建立不同的線條（會忽略進一步的字串資料行）。 |
| `timepivot`        | 在事件時間表上進行互動式導覽（在時間軸上旋轉）|

::: zone-end

::: zone pivot="azuremonitor"

|*視覺效果*     |描述|
|--------------------|-|
| `areachart`        | 區域圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 y 軸。 |
| `barchart`         | 第一個資料行是 X 軸，而且可以是文字、日期時間或數值。 其他資料行是數值，會顯示為水準去除。|
| `columnchart`      | `barchart`與垂直線相似，而不是水準的去除。|
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `scatterchart`     | 點圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 y 軸。 |
| `table`            | 預設值-結果會顯示為數據表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他（數值）資料行是 y 軸。 有一個字串資料行的值是用來「分組」數值欄位，並在圖表中建立不同的線條（會忽略進一步的字串資料行）。|

::: zone-end

* *PropertyName* /*PropertyValue*表示轉譯時要使用的其他資訊。
  所有屬性都是選擇性的。 支援的屬性包括：

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |每個量值的值是否會加入其所有前置項。 （ `true` 或 `false` ）|
|`kind`        |視覺效果種類的進一步詳述。 請參閱下列內容。                         |
|`legend`      |是否要顯示圖例（ `visible` 或 `hidden` ）。                       |
|`series`      |以逗號分隔的資料行清單，其合併的每一記錄值會定義記錄所屬的數列。|
|`ymin`        |要在 Y 軸上顯示的最小值。                                      |
|`ymax`        |要在 Y 軸上顯示的最大值。                                      |
|`title`       |視覺效果的標題（類型為 `string` ）。                                |
|`xaxis`       |如何調整 X 軸（ `linear` 或 `log` ）。                                      |
|`xcolumn`     |結果中的哪一個資料行用於 X 軸。                                |
|`xtitle`      |X 軸的標題（類型為 `string` ）。                                       |
|`yaxis`       |如何縮放 y 軸（ `linear` 或 `log` ）。                                      |
|`ycolumns`    |以逗號分隔的資料行清單，其中包含 x 資料行的每個值所提供的值。|
|`ysplit`      |如何分割多個視覺效果。 請參閱下列內容。                               |
|`ytitle`      |Y 軸的標題（類型為 `string` ）。                                       |
|`anomalycolumns`|僅與相關的屬性 `anomalychart` 。 以逗號分隔的資料行清單，將被視為異常數列，並在圖表上顯示為點|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |視覺效果種類的進一步詳述。 請參閱下列內容。                         |
|`series`      |以逗號分隔的資料行清單，其合併的每一記錄值會定義記錄所屬的數列。|
|`title`       |視覺效果的標題（類型為 `string` ）。                                |
|`yaxis`       |如何縮放 y 軸（ `linear` 或 `log` ）。                                      |

::: zone-end

有些視覺效果可以藉由提供屬性進行進一步的詳細討論 `kind` 。
它們是：

|*視覺效果*|`kind`             |描述                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |每個「區域」都代表自己。     |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |向右堆疊「區域」。        |
|               |`stacked100`       |將「區域」向右堆疊，並將每一個範圍延展到與其他人相同的寬度。|
|`barchart`     |`default`          |每個「橫條圖」都有自己的意思。      |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |堆疊「橫條」。                      |
|               |`stacked100`       |堆疊「bard」，並將每一個專案延展到與其他專案相同的寬度。|
|`columnchart`  |`default`          |每個「資料行」都代表自己。   |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |將「資料行」堆疊在另一個上方。|
|               |`stacked100`       |將「資料行」堆疊，並將每一個都延展到與其他專案相同的高度。|
|`piechart`     |`map`              |預期的資料行是 [經度，緯度] 或 [GeoJSON 點]、[彩色軸] 和 [數值]。 在 Kusto Explorer 桌面中支援。|
|`scatterchart` |`map`              |預期的資料行是 [經度，緯度] 或 [GeoJSON 點]。 數列資料行是選擇性的。 在 Kusto Explorer 桌面中支援。|

::: zone pivot="azuredataexplorer"

有些視覺效果支援分割成多個 y 軸值：

|`ysplit`  |描述                                                       |
|----------|------------------------------------------------------------------|
|`none`    |所有數列資料都會顯示單一 y 軸。 (預設值)       |
|`axes`    |單一圖表會顯示多個 y 軸（每個數列一個）。|
|`panels`  |會針對每個值轉譯一個圖表 `ycolumn` （最多一個限制）。|

::: zone-end

> [!NOTE]
> Render 運算子的資料模型會查看表格式資料，就像它有三種資料行：
>
> * X 軸資料行（以屬性工作表示 `xcolumn` ）。
> * 數列資料行（屬性所指示的任意數目的資料行 `series` ）。針對每一筆記錄，這些資料行的合併值會定義單一數列，而圖表的數列數目會與不同的組合值相同。
> * Y 軸資料行（屬性所指示的任意數目的資料行 `ycolumns` ）。
  針對每筆記錄，數列在 y 軸資料行中有多個測量（在圖表中為「點」）。

> [!TIP]
> 
> * 使用 `where` `summarize` 和 `top` 來限制您所顯示的磁片區。
> * 排序資料以定義 X 軸的順序。
> * 使用者代理程式可自由「猜測」查詢未指定的屬性值。 特別的是，在結果的架構中具有「無意義」的資料行，可能會轉譯成猜測錯誤。 當這種情況發生時，請嘗試投射這類資料行。 

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[教學課程中的轉譯範例](./tutorial.md#render-display-a-chart-or-table)。

[異常偵測](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)

::: zone-end
