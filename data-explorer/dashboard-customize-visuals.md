---
title: 自訂 Azure 資料總管儀表板視覺效果
description: 輕鬆自訂您的 Azure 資料總管儀表板視覺效果
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 22463307df0358228469fe480f5578222bed52bf
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379828"
---
# <a name="customize-azure-data-explorer-dashboard-visuals"></a>自訂 Azure 資料總管儀表板視覺效果

視覺效果是任何 Azure 資料總管儀表板不可或缺的一部分。 本檔詳細說明不同的視覺效果類型，並描述可供儀表板使用者自訂其視覺效果的各種選項。

> [!NOTE]
> 視覺自訂可在編輯模式中提供給儀表板編輯器。

## <a name="prerequisites"></a>先決條件

[使用 Azure 資料總管儀表板將資料視覺化](azure-data-explorer-dashboards.md)

## <a name="types-of-visuals"></a>視覺效果的類型

Azure 資料總管支援多種不同類型的視覺效果。 本節說明不同類型的視覺效果和結果集中所需的資料行，以繪製這些視覺效果。

### <a name="table"></a>Table

:::image type="content" source="media/dashboard-customize-visuals/table.png" alt-text="資料表視覺效果類型":::

根據預設，結果會顯示為數據表。 資料表視覺效果最適合用來呈現詳細或複雜的資料。

### <a name="bar-chart"></a>橫條圖

:::image type="content" source="media/dashboard-customize-visuals/bar-chart.png" alt-text="橫條圖視覺效果類型":::

橫條圖視覺效果需要在查詢結果中至少有兩個數據行。 預設會使用第一個資料行做為 y 軸。 此資料行可包含文字、日期時間或數值資料類型。 其他資料行會當做 X 軸使用，並包含要顯示為水平線條的數值資料類型。 橫條圖主要用於比較數值和名義離散值，其中每一行的長度代表其值。

### <a name="column-chart"></a>直條圖

:::image type="content" source="media/dashboard-customize-visuals/column-chart.png" alt-text="直條圖視覺效果類型":::

直條圖視覺效果需要在查詢結果中至少有兩個數據行。 預設會使用第一個資料行做為 X 軸。 此資料行可包含文字、日期時間或數值資料類型。 其他資料行會當做 y 軸使用，並包含要顯示為垂直線的數值資料類型。 直條圖可用於比較主要類別範圍中的特定子類別目錄專案，其中每一行的長度代表其值。

### <a name="area-chart"></a>區域圖

:::image type="content" source="media/dashboard-customize-visuals/area-chart.png" alt-text="區域圖視覺效果類型":::

區域圖視覺效果會顯示時間序列關聯性。 查詢的第一個資料行應該是數值，而且會當做 X 軸使用。 其他數值資料行則是 y 軸。 與折線圖不同的是，區域圖也會以視覺方式呈現音量。 區域圖適合用來指出不同資料集之間的變更。

### <a name="line-chart"></a>折線圖

:::image type="content" source="media/dashboard-customize-visuals/line-chart.png" alt-text="折線圖視覺效果類型":::

折線圖視覺效果是最基本的圖表類型。 查詢的第一個資料行應該是數值，而且會當做 X 軸使用。 其他數值資料行則是 y 軸。 折線圖會追蹤短時間和長期的變更。 當有較小的變更存在時，折線圖會比橫條圖更有用。

### <a name="stat"></a>統計

:::image type="content" source="media/dashboard-customize-visuals/stat.png" alt-text="stat 視覺效果類型":::

Stat 視覺效果只會顯示一個元素。 如果輸出中有多個資料行和資料列，stat 會顯示第一個資料行的第一個元素。 當您在儀表板上醒目提示 Kpi 時，Stat 卡片會很有用。

### <a name="pie-chart"></a>圓形圖

:::image type="content" source="media/dashboard-customize-visuals/pie-chart.png" alt-text="圓形圖視覺效果類型":::

圓形圖視覺效果需要在查詢結果中至少有兩個數據行。 預設會使用第一個資料行做為色軸。 此資料行可包含文字、日期時間或數值資料類型。 其他資料行將用來判斷每個配量的大小，並且包含數值資料類型。 圓形圖可用來呈現總計的類別和其比例的組合。

### <a name="scatter-chart"></a>散佈圖

:::image type="content" source="media/dashboard-customize-visuals/scatter-chart.png" alt-text="散佈圖視覺效果類型":::

在散佈圖視覺效果中，第一個資料行是 X 軸，而且必須是數值資料行。 其他數值資料行則是 y 軸。 散佈圖可用來觀察變數之間的關聯性。

### <a name="time-chart"></a>時間圖表 

:::image type="content" source="media/dashboard-customize-visuals/time-chart.png" alt-text="時間圖表視覺效果類型":::

時間圖表視覺效果是線條圖形類型。 查詢的第一個資料行是 X 軸，而且應該是 datetime。 其他數值資料行則是 y 軸。 一個字串資料行值會用來將數值資料行分組，並在圖表中建立不同的行。 其他字串資料行則會被忽略。 時間圖表視覺效果類似 [折線圖](#line-chart) ，但 X 軸永遠是時間。

### <a name="anomaly-chart"></a>異常圖表 

:::image type="content" source="media/dashboard-customize-visuals/anomaly-chart.png" alt-text="異常圖表視覺效果類型":::

異常圖表視覺效果類似于 [時間圖表](#time-chart)，但會使用函式來反白顯示異常 `series_decompose_anomalies` 。

### <a name="map"></a>對應

:::image type="content" source="media/dashboard-customize-visuals/map.png" alt-text="地圖視覺效果類型":::

若要呈現地圖視覺效果，查詢中需要四個數據行：
* 字串 (第一個資料行) 用於暫留標籤
* 經度 (real) 
* 真正) 的緯度 (
*  (int) 的反升尺寸。 如果不需要不同的大小，則此資料行可以是常數一。

地圖適用于使用地理座標將資料視覺化。 地圖視覺效果也有內建的縮放功能。

## <a name="customize-visuals"></a>自訂視覺效果

1. 選取 [儀表板] 功能表中的 [ **編輯** ]，以切換至編輯模式。
1. 若要存取卡片上的 [視覺效果自訂] 對話方塊，請按一下下拉式功能表 > **編輯卡片**]。 或者，當您使用 [ **新增查詢**] 建立新的卡片時，請選取 [ **編輯卡片**]。

:::image type="content" source="media/dashboard-customize-visuals/edit-card.png" alt-text="編輯卡片以進行視覺自訂":::

### <a name="select-properties-to-customize-visuals"></a>選取屬性以自訂視覺效果

使用下列屬性來自訂視覺效果。

:::image type="content" source="media/dashboard-customize-visuals/visual-customization-sidebar.png" alt-text="視覺自訂提要欄位":::

|區段  |描述 | 視覺效果類型
|---------|---------|-----|
|**一般**    |    選取 **堆疊** 或 **非堆疊** 的圖表格式  | 橫條圖、直條圖和區域圖 |
|**Data**    |   選取您視覺效果的 **Y 和 X 欄** 。 如果您希望平臺根據查詢結果自動選取資料行，請將選取專案保留為**推斷**    |橫條圖、直條圖、散佈圖和異常圖表|
|**圖例**    |   切換顯示或隱藏視覺效果上的圖例   |橫條圖、直條圖、區域圖、折線圖、散佈圖、異常和時間圖表 |
|**Y 軸**     |   允許自訂 Y 軸屬性： <ul><li>**標籤**：自訂標籤的文字。 </li><li>**最大值**：變更 Y 軸的最大值。  </li><li>**最小值**：變更 Y 軸的最小值。  </li></ul>      |橫條圖、直條圖、區域圖、折線圖、散佈圖、異常和時間圖表 |
|**X 軸**     |    允許自訂 X 軸屬性： <ul><li>**標籤**：自訂標籤的文字。 </li>     | 橫條圖、直條圖、區域圖、折線圖、散佈圖、異常和時間圖表|

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管儀表板中使用參數](dashboard-parameters.md)
* [在 Azure 資料總管中查詢資料](web-query-data.md) 