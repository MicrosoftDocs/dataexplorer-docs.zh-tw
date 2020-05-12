---
title: geo_distance_2points （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_distance_2points （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 64c61bcde7bfe02d55bb0f4a6719e9d7b1c1a681
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227412"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

計算兩個地理空間座標在地球上的最短距離。

**語法**

`geo_distance_2points(`*p1_longitude* `, `*p1_latitude* `, `*p2_longitude* `, `*p2_latitude*`)`

**引數**

* *p1_longitude*：第一個地理空間座標，經度值，以度為單位。 有效的值為實數，且範圍為 [-180，+ 180]。
* *p1_latitude*：第一個地理空間座標，以度為單位的緯度值。 有效的值為實數，且範圍為 [-90，+ 90]。
* *p2_longitude*：第二個地理空間座標、經度值（以度為單位）。 有效的值為實數，且範圍為 [-180，+ 180]。
* *p2_latitude*：第二個地理空間座標，以度為單位的緯度值。 有效的值為實數，且範圍為 [-90，+ 90]。

**傳回**

地球上兩個地理位置之間的最短距離（以量為單位）。 如果座標無效，查詢將會產生 null 結果。

> [!NOTE]
> * 地理空間座標會以[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系統來表示。
> * 用來測量地球距離的[geodetic 基準](https://en.wikipedia.org/wiki/Geodetic_datum)是一個球體。

**範例**

下列範例會尋找西雅圖和洛杉磯之間的最短距離。

:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="西雅圖與洛杉磯之間的距離":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

以下是從西雅圖到倫敦的最短路徑近似值。 這一行包含沿著 LineString 的座標以及500計量中的座標。

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="西雅圖至倫敦 LineString":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

下列範例會尋找在兩個座標之間的最短距離介於1到11個計量之間的所有資料列。

<!-- csl: https://help.kusto.windows.net/Samples -->
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

下列範例會傳回 null 結果，因為座標輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |
