---
title: 轉譯運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的轉譯運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 069733d2215257106ede58f4fd6e4f2a923a8982
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243116"
---
# <a name="render-operator"></a>render 運算子

指示使用者代理程式以特定方式轉譯查詢的結果。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * 轉譯運算子應該是查詢中的最後一個運算子，而且只能搭配產生單一表格式資料流程結果的查詢使用。
> * 轉譯運算子不會修改資料。 它會在結果的擴充屬性中插入批註 ( 「視覺效果」 ) 。 批註包含運算子在查詢中提供的資訊。
> * 視覺效果資訊的解讀是由使用者代理程式所完成。 不同的代理程式 (例如 Kusto，Kusto. WebExplorer) 可能支援不同的視覺效果。

## <a name="syntax"></a>語法

*T* `|` `render` *視覺效果* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

其中：

* *視覺效果* 會指出要使用的視覺效果類型。 支援的值為：

::: zone pivot="azuredataexplorer"

|*視覺效果*     |描述|
|--------------------|-|
| `anomalychart`     | 類似于時間圖表，但使用[series_decompose_anomalies](./series-decompose-anomaliesfunction.md)函式來[強調異常](./samples.md#get-more-out-of-your-data-in-kusto-with-machine-learning)。 |
| `areachart`        | 區域圖。 第一個資料行是 X 軸，而且必須是數值資料行。 其他數值資料行則是 y 軸。 |
| `barchart`         | 第一個資料行是 X 軸，而且可以是文字、日期時間或數值。 其他資料行是數值，以水準條紋顯示。|
| `card`             | 第一個結果記錄會視為一組純量值，並顯示為卡片。 |
| `columnchart`      | 像 `barchart` 是分隔號紋，而不是水準條紋。|
| `ladderchart`      | 最後兩個數據行是 X 軸，其他資料行則是 y 軸。|
| `linechart`        | 折線圖。 第一個資料行是 X 軸，而且必須是數值資料行。 其他數值資料行則是 y 軸。 |
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `pivotchart`       | 顯示樞紐分析表和圖表。 使用者可以透過互動方式選取資料、資料行、資料列和各種圖表類型。 |
| `scatterchart`     | 點圖形。 第一個資料行是 X 軸，而且應該是數值資料行。 其他數值資料行則是 y 軸。 |
| `stackedareachart` | 堆疊區域圖。 第一個資料行是 X 軸，而且必須是數值資料行。 其他數值資料行則是 y 軸。 |
| `table`            | 預設值-結果會顯示為數據表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他 (數值) 資料行是 y 軸。 有一個字串資料行的值是用來「分組」數值資料行，並在圖表中建立不同的線條 (會忽略) 的其他字串資料行。 |
| `timepivot`        | 在事件時間軸上進行互動式導覽 (在時間軸上進行樞紐分析)|

::: zone-end

::: zone pivot="azuremonitor"

|*視覺效果*     |描述|
|--------------------|-|
| `areachart`        | 區域圖。 第一個資料行是 X 軸，而且必須是數值資料行。 其他數值資料行則是 y 軸。 |
| `barchart`         | 第一個資料行是 X 軸，而且可以是文字、日期時間或數值。 其他資料行是數值，以水準條紋顯示。|
| `columnchart`      | 像 `barchart` 是分隔號紋，而不是水準條紋。|
| `piechart`         | 第一個資料行是色彩座標軸，第二個資料行是數值。 |
| `scatterchart`     | 點圖形。 第一個資料行是 X 軸，而且必須是數值資料行。 其他數值資料行則是 y 軸。 |
| `table`            | 預設值-結果會顯示為數據表。|
| `timechart`        | 折線圖。 第一個資料行是 X 軸，而且應該是日期時間。 其他 (數值) 資料行是 y 軸。 有一個字串資料行的值是用來「分組」數值資料行，並在圖表中建立不同的線條 (會忽略) 的其他字串資料行。|

::: zone-end

* *PropertyName* /*PropertyValue*表示轉譯時要使用的其他資訊。
  所有屬性都是選擇性的。 支援的屬性包括：

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |每個量值的值是否會新增至其所有前身。  (`true` 或 `false`) |
|`kind`        |視覺效果種類的進一步詳述。 請參閱下文。                         |
|`legend`      |是否要顯示圖例，或不 (`visible` 或 `hidden`) 。                       |
|`series`      |以逗號分隔的資料行清單，其結合的每一筆記錄值會定義記錄所屬的數列。|
|`ymin`        |要顯示在 Y 軸上的最小值。                                      |
|`ymax`        |要顯示在 Y 軸上的最大值。                                      |
|`title`       |) 類型之視覺效果 (的標題 `string` 。                                |
|`xaxis`       |如何調整 X 軸 (`linear` 或 `log`) 。                                      |
|`xcolumn`     |結果中的哪一個資料行用於 X 軸。                                |
|`xtitle`      |) 類型的 X 軸 (的標題 `string` 。                                       |
|`yaxis`       |如何調整 y 軸 (`linear` 或 `log`) 。                                      |
|`ycolumns`    |以逗號分隔的資料行清單，其中包含每個 x 資料行值所提供的值。|
|`ysplit`      |如何分割多個視覺效果。 請參閱下文。                               |
|`ytitle`      |) 類型的 y 軸 (的標題 `string` 。                                       |
|`anomalycolumns`|僅與相關聯的屬性 `anomalychart` 。 以逗號分隔的資料行清單，將視為異常數列，並顯示為圖表上的點|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |視覺效果種類的進一步詳述。 請參閱下文。                         |
|`series`      |以逗號分隔的資料行清單，其結合的每一筆記錄值會定義記錄所屬的數列。|
|`title`       |) 類型之視覺效果 (的標題 `string` 。                                |
|`yaxis`       |如何調整 y 軸 (`linear` 或 `log`) 。                                      |

::: zone-end

藉由提供屬性，可以進一步地詳細討論一些視覺效果 `kind` 。
這些警告是：

|*視覺效果*|`kind`             |描述                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |每個「區域」都有自己的意思。     |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |向右堆疊「區域」。        |
|               |`stacked100`       |將「區域」堆疊到右邊，並將每個區域延展至與其他人相同的寬度。|
|`barchart`     |`default`          |每個 "bar" 都有自己的意思。      |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |堆疊「橫條」。                      |
|               |`stacked100`       |堆疊 "bard"，並將每個專案延展到與其他人相同的寬度。|
|`columnchart`  |`default`          |每一個「資料行」都有自己的意思。   |
|               |`unstacked`        |與 `default` 相同。                 |
|               |`stacked`          |將 "columns" 堆疊在另一個。|
|               |`stacked100`       |堆疊「資料行」，並將每個資料行延展到與其他人相同的高度。|
|`scatterchart` |`map`              |預期的資料行是 [經度、緯度] 或 GeoJSON 點。 數列資料行是選擇性的。|
|`piechart`     |`map`              |預期的資料行是 [經度、緯度] 或 GeoJSON 點、色彩軸和數值。 在 Kusto Explorer desktop 中受到支援。|

::: zone pivot="azuredataexplorer"

有些視覺效果支援分割成多個 y 軸值：

|`ysplit`  |描述                                                       |
|----------|------------------------------------------------------------------|
|`none`    |所有數列資料都會顯示單一 y 軸。 (預設值)       |
|`axes`    |單一圖表會以多個 y 軸顯示 (每個數列) 一個。|
|`panels`  |針對每個值轉譯一個圖表， `ycolumn` (最多) 的部分限制。|

::: zone-end

> [!NOTE]
> 轉譯運算子的資料模型會查看表格式資料，就像它有三種資料行一樣：
>
> * X 軸資料行 (由 `xcolumn` 屬性) 表示。
> * 數列資料行 (由屬性工作表示的任意數目的資料行 `series` ) 。針對每一筆記錄，這些資料行的合併值會定義單一數列，而圖表的數列數量會與相異的組合值相同。
> * Y 軸資料行 (由屬性) 指出的任意數目的資料行 `ycolumns` 。
  針對每一筆記錄，此數列有許多度量 ( 「點」在圖表中) ，如同 y 軸資料行一樣。

> [!TIP]
> 
> * 使用 `where` `summarize` 和 `top` 來限制您所顯示的磁片區。
> * 排序資料來定義 X 軸的順序。
> * 使用者代理程式可自由「猜測」查詢未指定的屬性值。 尤其是，在結果的架構中具有「不方便」的資料行，可能會將結果轉譯為猜測錯誤。 當這種情況發生時，請嘗試投射這類資料行。 

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

[教學課程中的轉譯範例](./tutorial.md#render-display-a-chart-or-table)。

[異常偵測](./samples.md#get-more-out-of-your-data-in-kusto-with-machine-learning)

::: zone-end
