---
title: render 運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 render 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5670f3f9c7aa8b3d6b10f88433d19246e2daf6d6
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783330"
---
# <a name="render-operator"></a>render 運算子

指示使用者代理程式以特定方式呈現查詢的結果。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * render 運算子應該是查詢中的最後一個運算子，而且只能搭配會產生單一表格式資料流結果的查詢來使用。
> * render 運算子不會修改資料。 其會將註釋 (「視覺效果」) 插入到結果的擴充屬性中。 註釋內包含查詢中的運算子所提供的資訊。
> * 視覺效果資訊的解讀則由使用者代理程式進行。 不同的代理程式 (例如 Kusto.Explorer,Kusto.WebExplorer) 可能會支援不同的視覺效果。

## <a name="syntax"></a>語法

*T* `|` `render` *Visualization* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`]

其中：

* *Visualization* 會指出要使用的視覺效果種類。 支援的值為：

::: zone pivot="azuredataexplorer"

|*視覺效果*     |描述|
|--------------------|-|
| `anomalychart`     | 類似於時間圖，但會使用 [series_decompose_anomalies](./series-decompose-anomaliesfunction.md) 函式來[醒目提示異常](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)。 |
| `areachart`        | 區域圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 Y 軸。 |
| `barchart`         | 第一個資料行是 X 軸，而且可以是文字、日期時間或數值。 其他資料行是數值，會顯示為橫條。|
| `card`             | 第一個結果記錄會視為純量值集合，並顯示為卡片。 |
| `columnchart`      | 如同 `barchart`，但具有直條，而不是橫條。|
| `ladderchart`      | 最後兩個資料行是 X 軸，其他資料行則是 Y 軸。|
| `linechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 Y 軸。 |
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `pivotchart`       | 顯示樞紐分析表和圖表。 使用者可以透過互動方式選取資料、資料行、資料列和各種圖表類型。 |
| `scatterchart`     | 點圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 Y 軸。 |
| `stackedareachart` | 堆疊區域圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 Y 軸。 |
| `table`            | 預設值 - 結果會顯示為資料表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他 (數值) 資料行則為 Y 軸。 有一個字串資料行，其值會用來「分組」數值資料行，並在圖表中建立不同的線條 (此外的字串資料行則會遭到忽略)。 |
| `timepivot`        | 在事件時間軸上進行互動式導覽 (在時間軸上進行樞紐分析)|

::: zone-end

::: zone pivot="azuremonitor"

|*視覺效果*     |描述|
|--------------------|-|
| `areachart`        | 區域圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 Y 軸。 |
| `barchart`         | 第一個資料行是 X 軸，而且可以是文字、日期時間或數值。 其他資料行是數值，會顯示為橫條。|
| `columnchart`      | 如同 `barchart`，但具有直條，而不是橫條。|
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `scatterchart`     | 點圖。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則為 Y 軸。 |
| `table`            | 預設值 - 結果會顯示為資料表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他 (數值) 資料行則為 Y 軸。 有一個字串資料行，其值會用來「分組」數值資料行，並在圖表中建立不同的線條 (此外的字串資料行則會遭到忽略)。|

::: zone-end

* *PropertyName*/*PropertyValue* 會指出在呈現時所要使用的其他資訊。
  所有屬性都是選擇性的。 支援的屬性包括：

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |是否要將每個量值的值新增至其所有前置項。 (`true` 或 `false`)|
|`kind`        |進一步詳述視覺效果種類。 請參閱下文。                         |
|`legend`      |是否要顯示圖例 (`visible` 或 `hidden`)。                       |
|`series`      |以逗號分隔的資料行清單，其合併的每一筆記錄值會定義記錄所屬的數列。|
|`ymin`        |要在 Y 軸上顯示的最小值。                                      |
|`ymax`        |要在 Y 軸上顯示的最大值。                                      |
|`title`       |視覺效果的標題 (屬於 `string` 類型)。                                |
|`xaxis`       |如何調整 X 軸 (`linear` 或 `log`)。                                      |
|`xcolumn`     |要將結果中的哪一個資料行用於 X 軸。                                |
|`xtitle`      |X 軸的標題 (屬於 `string` 類型)。                                       |
|`yaxis`       |如何調整 Y 軸 (`linear` 或 `log`)。                                      |
|`ycolumns`    |以逗號分隔的資料行清單，其包含 X 資料行的每個值所提供的值。|
|`ysplit`      |如何分割多個視覺效果。 請參閱下文。                               |
|`ytitle`      |Y 軸的標題 (屬於 `string` 類型)。                                       |
|`anomalycolumns`|僅與 `anomalychart` 相關的屬性。 以逗號分隔的資料行清單，會將其視為異常數列，並在圖表上顯示為點|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |進一步詳述視覺效果種類。 請參閱下文。                         |
|`series`      |以逗號分隔的資料行清單，其合併的每一筆記錄值會定義記錄所屬的數列。|
|`title`       |視覺效果的標題 (屬於 `string` 類型)。                                |
|`yaxis`       |如何調整 Y 軸 (`linear` 或 `log`)。                                      |

::: zone-end

有些視覺效果可以藉由提供 `kind` 屬性來進一步詳述。
這些警告是：

|*視覺效果*|`kind`             |描述                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |每個「區域」都獨立存在。     |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |向右堆疊「區域」。        |
|               |`stacked100`       |向右堆疊「區域」，並將每一個區域延展為與其他區域相同的寬度。|
|`barchart`     |`default`          |每個「橫條」都獨立存在。      |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |堆疊「橫條」。                      |
|               |`stacked100`       |堆疊「橫條」，並將每一個橫條延展為與其他橫條相同的寬度。|
|`columnchart`  |`default`          |每個「直條」都獨立存在。   |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |將「直條」堆疊在另一個直條上方。|
|               |`stacked100`       |堆疊「直條」，並將每一個直條延展為與其他直條相同的高度。|
|`scatterchart` |`map`              |預期的資料行是 [經度,緯度] 或 GeoJSON 點。 數列資料行是選擇性的。|
|`piechart`     |`map`              |預期的資料行是 [經度,緯度] 或 GeoJSON 點、彩色軸和數值。 Kusto Explorer 桌面可支援。|

::: zone pivot="azuredataexplorer"

有些視覺效果支援分割成多個 Y 軸值：

|`ysplit`  |描述                                                       |
|----------|------------------------------------------------------------------|
|`none`    |所有數列資料都會顯示單一 Y 軸。 (預設值)       |
|`axes`    |單一圖表會顯示多個 Y 軸 (每個數列一個)。|
|`panels`  |會針對每個 `ycolumn` 值 (有一定上限) 呈現一個圖表。|

::: zone-end

> [!NOTE]
> render 運算子的資料模型會查看表格式資料，情形就像其有三種資料行：
>
> * X 軸資料行 (以 `xcolumn` 屬性表示)。
> * 數列資料行 (以 `series` 屬性表示的任意數目資料行)。針對每一筆記錄，這些資料行的合併值會定義單一數列，而圖表的數列數目會與相異合併值相同。
> * Y 軸資料行 (以 `ycolumns` 屬性表示的任意數目資料行)。
  針對每一筆記錄，數列的量值 (圖表中的「點」) 數目會與 Y 軸資料行相同。

> [!TIP]
> 
> * 使用 `where`、`summarize` 和 `top` 來限制您顯示的資料量。
> * 將資料排序以定義 X 軸的順序。
> * 使用者代理程式可自由「猜測」查詢未指定的屬性值。 特別的是，若結果的結構描述中有「無意義」的資料行，則可能會讓其猜測錯誤。 當這種情況發生時，請嘗試排除這類資料行。 

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[教學課程中的呈現範例](./tutorial.md#displaychartortable)

[異常偵測](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)

::: zone-end
