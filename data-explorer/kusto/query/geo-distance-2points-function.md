---
title: geo_distance_2points() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的geo_distance_2points()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 4b63ffaee8123b4ee281426f30f757fbe5165e75
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663714"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

計算地球上兩個地理空間座標之間的最短距離。

**語法**

`geo_distance_2points(`*p1_longitude** * *p1_longitudep1_latitudep2_longitudep2_latitude* `, ` `, ` `, `* *`)`

**引數**

* *p1_longitude:* 第一地理空間座標,經度值(以度表示)。 有效值是實數,範圍為 [-180, [180]。
* *p1_latitude:* 第一地理空間座標,緯度值(以度表示)。 有效值是實數,範圍為 [-90, [90]。
* *p2_longitude:* 第二地理空間座標,經度值(以度表示)。 有效值是實數,範圍為 [-180, [180]。
* *p2_latitude:* 第二地理空間座標,緯度值(以度表示)。 有效值是實數,範圍為 [-90, [90]。

**傳回**

地球上兩個地理位置之間的最短距離(以米為單位)。 如果座標無效,查詢將生成空結果。

> [!NOTE]
> * 地理空間座標被解釋為由[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系表示。
> * 測量地球距離的[大地測量基準是](https://en.wikipedia.org/wiki/Geodetic_datum)一個球體。

**範例**

下面的示例查找西雅圖和洛杉磯之間的最短距離。

:::image type="content" source="images/queries/geo/distance_2points_seattle_los_angeles.png" alt-text="西雅圖和洛杉磯之間的距離":::

```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

這裡是從西雅圖到倫敦的最短路徑的近似值。 該線由沿線弦的座標組成,距離線線線不到 500 米。

:::image type="content" source="images/queries/geo/line_seattle_london.png" alt-text="西雅圖到倫敦線串":::

```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

下面的示例查找兩個座標之間的最短距離介於 1 和 11 米之間的所有行。
```kusto
StormEvents
| extend distance_1_to_11m = geo_distance_2points(BeginLon, BeginLat, EndLon, EndLat)
| where distance_1_to_11m between (1 .. 11)
| project distance_1_to_11m
```

| distance_1_to_11m |
|-------------------|
| 10.5723100154958  |
| 7.92153588248414  |

以下範例返回 null 結果,因為座標輸入無效。
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |