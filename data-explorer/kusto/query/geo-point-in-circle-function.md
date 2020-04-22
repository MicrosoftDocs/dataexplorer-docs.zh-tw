---
title: geo_point_in_circle() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的geo_point_in_circle()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: ca3ca8a1ac2299c43888ac827d46c25d02e4999d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663790"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

計算地理空間座標是否位於地球上的一個圓內。

**語法**

`geo_point_in_circle(`*p_longitude*`, ` *pc_latitude* p_longitudep_latitudepc_longitudepc_latitudec_radius * * `, ` `, ` `, `* * * *`)`

**引數**

* *p_longitude:* 地理空間座標經度值(以度表示)。 有效值是實數,範圍為 [-180, [180]。
* *p_latitude:* 地理空間座標緯度值(以度表示)。 有效值是實數,範圍為 [-90, [90]。
* *pc_longitude*: 圓形中心地理空間座標經度值(以度表示)。 有效值是實數,範圍為 [-180, [180]。
* *pc_latitude*: 圓形中心地理空間座標緯度值(以度表示)。 有效值是實數,範圍為 [-90, [90]。
* *c_radius*: 圓半徑(以米為單位)。 有效值必須為正。

**傳回**

指示地理空間座標是否位於圓內。 如果座標或圓無效,則查詢將生成空結果。

> [!NOTE]
>* 地理空間座標被解釋為由[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系表示。
>* 測量地球距離的[大地測量基準是](https://en.wikipedia.org/wiki/Geodetic_datum)一個球體。
>* 圓是地球上的球形帽。 蓋的半徑沿球體表面測量。

**範例**

以下查詢查找半徑為 18 km 的圓定義區域中的所有位置,其中心位於 +-122.317404、47.609119] 座標。

:::image type="content" source="images/queries/geo/circle_seattle.png" alt-text="西雅圖附近的地點":::

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

奧蘭多的風暴事件 事件由奧蘭多座標篩選,在 100 公里內,按事件類型和哈希聚合。

:::image type="content" source="images/queries/geo/orlando_storm_events.png" alt-text="奧蘭多的風暴事件":::

```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

下面的示例顯示紐約計程車皮卡附近的某個位置,並在10米內。 相關取件按哈希聚合。

:::image type="content" source="images/queries/geo/circle_junction.png" alt-text="紐約計程車附近的皮卡":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

以下範例將返回 true。
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

以下示例將返回 false。
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

以下範例將傳回 null 結果,因為座標輸入無效。
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

由於圓半徑輸入無效,以下示例將返回空結果。
```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||