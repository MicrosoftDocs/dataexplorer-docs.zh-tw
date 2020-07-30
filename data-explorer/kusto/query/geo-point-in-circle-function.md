---
title: geo_point_in_circle （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_point_in_circle （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 1e94e8eca72e6cb679a84e7b91ea376b9ec4b29c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347811"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

計算地理空間座標是否在地球上的圓圈內。

## <a name="syntax"></a>語法

`geo_point_in_circle(`*p_longitude* `, `*p_latitude* `, `*pc_longitude* `, `*pc_latitude* `, `*c_radius*`)`

## <a name="arguments"></a>引數

* *p_longitude*：地理空間座標經度值（以度為單位）。 有效的值為實數，且範圍為 [-180，+ 180]。
* *p_latitude*：地理空間座標緯度值（以度為單位）。 有效的值為實數，且範圍為 [-90，+ 90]。
* *pc_longitude*：圓形中心地理空間座標經度值（以度為單位）。 有效的值為實數，且範圍為 [-180，+ 180]。
* *pc_latitude*：圓形中心地理空間座標緯度值（以度為單位）。 有效的值為實數，且範圍為 [-90，+ 90]。
* *c_radius*：圓形半徑，以量為單位。 有效值必須是正數。

## <a name="returns"></a>傳回

指出地理空間座標是否在圓形內。 如果座標或圓形無效，查詢將會產生 null 結果。

> [!NOTE]
>* 地理空間座標會以[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系統來表示。
>* 用來測量地球距離的[geodetic 基準](https://en.wikipedia.org/wiki/Geodetic_datum)是一個球體。
>* 圓形是地球上的球面帽。 端點的半徑會沿著球體的表面測量。

## <a name="examples"></a>範例

下列查詢會尋找區域中由下列圓形所定義的所有位置：半徑為18公里，中心為 [-122.317404，47.609119] 座標。

:::image type="content" source="images/geo-point-in-circle-function/circle-seattle.png" alt-text="西雅圖附近的地點":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(longitude:real, latitude:real, place:string)
[
    real(-122.317404), 47.609119, 'Seattle',                   // In circle 
    real(-123.497688), 47.458098, 'Olympic National Forest',   // In exterior of circle  
    real(-122.201741), 47.677084, 'Kirkland',                  // In circle
    real(-122.443663), 47.247092, 'Tacoma',                    // In exterior of circle
    real(-122.121975), 47.671345, 'Redmond',                   // In circle
]
| where geo_point_in_circle(longitude, latitude, -122.317404, 47.609119, 18000)
| project place
```

|place|
|---|
|Seattle|
|Kirkland|
|Redmond|

奧蘭多中的風暴事件。 事件會在奧蘭多座標中以100公里進行篩選，並依事件種類和雜湊進行匯總。

:::image type="content" source="images/geo-point-in-circle-function/orlando-storm-events.png" alt-text="奧蘭多中的風暴事件":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

下列範例顯示在特定位置的10個計量範圍內的 NY 計程車 pickups。 相關的 pickups 會依雜湊進行匯總。

:::image type="content" source="images/geo-point-in-circle-function/circle-junction.png" alt-text="Pickups 的紐約州計程車":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

下列範例會傳回 true。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

下列範例會傳回 false。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

下列範例會傳回 null 結果，因為座標輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

下列範例會傳回 null 結果，因為 circle 半徑輸入無效。

```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||
