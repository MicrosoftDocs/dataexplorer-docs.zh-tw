---
title: geo_distance_point_to_line （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_distance_point_to_line （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 304b40a00fd471b7735ff11c01bdaa8b6ca3a8ec
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227435"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

計算在地球上座標和線條之間的最短距離。

**語法**

`geo_distance_point_to_line(`*經度* `, `*緯度* `, `*lineString*`)`

**引數**

* *經度*：地理空間座標經度值（以度為單位）。 有效的值為實數，且範圍為 [-180，+ 180]。
* *緯度*：地理空間座標緯度值（以度為單位）。 有效的值為實數，且範圍為 [-90，+ 90]。
* *lineString*： [GeoJSON 格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的行。

**傳回**

座標和地球上線條之間的最短距離（以量為單位）。 如果座標或 lineString 無效，查詢將會產生 null 結果。

> [!NOTE]
> * 地理空間座標會以[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系統來表示。
> * 用來測量地球距離的[geodetic 基準](https://en.wikipedia.org/wiki/Geodetic_datum)是一個球體。 線條邊緣會 geodesics 在球體上。

**LineString 定義和條件約束**

dynamic （{"type"： "LineString"，"座標"： [[lng_1，lat_1]，[lng_2，lat_2],..., [lng_N，lat_N]]}）

* LineString 座標陣列必須包含至少兩個專案。
* 座標 [經度，緯度] 必須是有效的，其中經度是範圍 [-180，+ 180] 中的實數，而緯度是範圍 [-90，+ 90] 中的實際數位。
* 邊緣長度必須小於180度。 將選擇兩個頂點之間的最短邊緣。

> [!TIP]
> 為獲得較佳的效能，請使用常值行。

**範例**

下列範例會尋找北內華達和附近道路之間的最短距離。

:::image type="content" source="images/geo-distance-point-to-line-function/distance-point-to-line.png" alt-text="北內華達的機場與道路之間的距離":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

南 coast US 中的風暴事件。 系統會根據定義的支援腳步行，將事件篩選成5公里的最大距離。

:::image type="content" source="images/geo-distance-point-to-line-function/us-south-coast-storm-events.png" alt-text="美國南部 coast 中的風暴事件":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

紐約州計程車 pickups。 Pickups 是依定義行中 0.1 m 的最大距離進行篩選。

:::image type="content" source="images/geo-distance-point-to-line-function/park-ave-ny-road.png" alt-text="美國南部 coast 的風暴事件":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

下列範例會傳回 null 結果，因為不正確 LineString 輸入。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

下列範例會傳回 null 結果，因為座標輸入無效。

```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |
