---
title: 成成運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的呈現運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 645860405a165f3304f655e42d2ced9b8777b8d0
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765484"
---
# <a name="render-operator"></a>render 運算子

指示使用者代理以特定方式呈現查詢的結果。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * 呈現運算元應該是查詢中的最後一個運算符,並且僅用於生成單個表格數據流結果的查詢。
> * 渲染運算符不修改數據。 它將註釋("可視化")注入結果的擴展屬性。 註釋包含運算符在查詢中提供的資訊。
> * 可視化資訊的解釋由使用者代理完成。 不同的代理(如 Kusto.Explorer、Kusto.WebExplorer)可能支援不同的可視化效果。

**語法**

*T*`|`T`with``(``render`視覺*PropertyValue*化 [ 屬性名稱屬性值 ] ...]*PropertyName*`=``,`*Visualization*`)`]

其中：

* *可視化*表示要使用的可視化類型。 支援的值為：

::: zone pivot="azuredataexplorer"

|*視覺效果*     |描述|
|--------------------|-|
| `anomalychart`     | 與時程圖表類似,但使用[series_decompose_anomalies](./series-decompose-anomaliesfunction.md)函數[突顯異常](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)。 |
| `areachart`        | 面積圖。 第一列是 x 軸,應為數位列。 其他數位列是 y 軸。 |
| `barchart`         | 第一列是 x 軸,可以是文本、日期時間或數位。 其他列是數字,顯示為水準條。|
| `card`             | 第一個結果記錄被視為一組標量值,並顯示為卡片。 |
| `columnchart`      | 與`barchart`垂直條條而不是水準條類似。|
| `ladderchart`      | 最後兩列是 x 軸,其他列是 y 軸。|
| `linechart`        | 折線圖。 第一列是 x 軸,應為數位列。 其他數位列是 y 軸。 |
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `pivotchart`       | 顯示數據透視表和圖表。 用戶可以以交互方式選擇數據、列、行和各種圖表類型。 |
| `scatterchart`     | 點圖。 第一列是 x 軸,應為數位列。 其他數位列是 y 軸。 |
| `stackedareachart` | 堆疊面積圖。 第一列是 x 軸,應為數位列。 其他數位列是 y 軸。 |
| `table`            | 預設值 - 結果顯示為表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他(數位)列是 y 軸。 有一個字串列的值用於「分組」數位列並在圖表中創建不同的行(忽略進一步的字串列)。 |
| `timepivot`        | 事件時間線的互動式導覽(在時間軸上旋轉)|

::: zone-end

::: zone pivot="azuremonitor"

|*視覺效果*     |描述|
|--------------------|-|
| `areachart`        | 面積圖。 第一列是 x 軸,應為數位列。 其他數位列是 y 軸。 |
| `barchart`         | 第一列是 x 軸,可以是文本、日期時間或數位。 其他列是數字,顯示為水準條。|
| `columnchart`      | 與`barchart`垂直條條而不是水準條類似。|
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `scatterchart`     | 點圖。 第一列是 x 軸,應為數位列。 其他數位列是 y 軸。 |
| `table`            | 預設值 - 結果顯示為表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他(數位)列是 y 軸。 有一個字串列的值用於「分組」數位列並在圖表中創建不同的行(忽略進一步的字串列)。|

::: zone-end

* *屬性名稱*/*屬性值*指示渲染時要使用的其他資訊。
  所有屬性都是可選的。 支援的屬性包括：

::: zone pivot="azuredataexplorer"

|*屬性名稱*|*屬性值*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |每個度量值的值是否添加到其所有前置值。 (`true``false`或 )|
|`kind`        |進一步闡述可視化類型。 請參閱下列內容。                         |
|`legend`      |是否顯示圖例(`visible``hidden`或 )。                       |
|`series`      |合併每個記錄值定義記錄所屬的序列的欄號分隔清單。|
|`ymin`        |要在 Y 軸上顯示的最小值。                                      |
|`ymax`        |要在 Y 軸上顯示的最大值。                                      |
|`title`       |可視化的標題(類型`string`)。                                |
|`xaxis`       |如何縮放 x`linear`軸`log`( 或 )                                      |
|`xcolumn`     |結果中的哪一列用於 x 軸。                                |
|`xtitle`      |x 軸的標題(`string`類型 )。                                       |
|`yaxis`       |如何縮放 y`linear`軸`log`( 或 )。                                      |
|`ycolumns`    |由 x 列每個值提供的值組成的欄號分隔清單。|
|`ysplit`      |如何拆分可視化效果的倍數。 請參閱下列內容。                               |
|`ytitle`      |y 軸的標題(`string`類型 )。                                       |
|`anomalycolumns`|僅與`anomalychart`相關的屬性。 逗號分隔的列清單,此清單將被視出異常序列,並顯示為圖表上的點|

::: zone-end

::: zone pivot="azuremonitor"

|*屬性名稱*|*屬性值*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |進一步闡述可視化類型。 請參閱下列內容。                         |
|`series`      |合併每個記錄值定義記錄所屬的序列的欄號分隔清單。|
|`title`       |可視化的標題(類型`string`)。                                |
|`yaxis`       |如何縮放 y`linear`軸`log`( 或 )。                                      |

::: zone-end

某些可視化效果可以通過`kind`提供 屬性來進一步闡述。
它們是：

|*視覺效果*|`kind`             |描述                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |每個"區域"都獨立地站立。     |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |向右堆疊"        |
|               |`stacked100`       |將「區域」向右堆疊,並將每個區域拉伸到與其他區域相同的寬度。|
|`barchart`     |`default`          |每個"酒吧"都獨立站立。      |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |堆疊"柱線"。                      |
|               |`stacked100`       |堆疊"bard",並將每個"巴德"拉伸到與其他相同的寬度。|
|`columnchart`  |`default`          |每個"列"都獨立站立。   |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |將「列」堆疊在一個列上。|
|               |`stacked100`       |堆疊"列",並將每個列拉伸到與其他列相同的高度。|
|`piechart`     |`map`              |預期列是 [經度、緯度] 或 GeoJSON 點、顏色軸和數位。 在 Kusto 資源管理器桌面中支援。|
|`scatterchart` |`map`              |預期列是 [經度、緯度] 或 GeoJSON 點。 系列列是可選的。 在 Kusto 資源管理器桌面中支援。|

::: zone pivot="azuredataexplorer"

某些視覺化效果支援分割的多個 y 軸值:

|`ysplit`  |描述                                                       |
|----------|------------------------------------------------------------------|
|`none`    |將顯示一個 y 軸,用於所有系列數據。 (預設值)       |
|`axes`    |單個圖表顯示多個 y 軸(每個系列一個)。|
|`panels`  |為每個`ycolumn`值呈現一個圖表(最多一些限制)。|

::: zone-end

> [!NOTE]
> 成像運算子的資料模型檢視表格資料時,就好像它有三種類型的欄一樣:
>
> * x 軸列(`xcolumn`由 屬性指示)。
> * 序列列(屬性指示的`series`任意數量的列。對於每個記錄,這些列的組合值定義單個系列,圖表具有與具有不同組合值相同的序列數。
> * y 軸列(屬性指示`ycolumns`的 任意數量的列)。
  對於每個記錄,該系列的測量值(圖表中的"點")與 y 軸列的測量值相同。

> [!TIP]
> 
> * 使用`where``summarize`和`top`來限制顯示的卷。
> * 對資料進行排序以定義 x 軸的順序。
> * 使用者代理可以自由地「猜測」查詢未指定的屬性的值。 特別是,在結果的架構中包含"無趣"列可能會轉化為它們猜錯。 發生這種情況時,請嘗試投影此類列。 

**範例**

```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[在教學中呈現範例](./tutorial.md#render-display-a-chart-or-table)。

[異常檢測](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)

::: zone-end
