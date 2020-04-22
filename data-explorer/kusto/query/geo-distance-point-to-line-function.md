---
title: geo_distance_point_to_line() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的geo_distance_point_to_line()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 34c6811ed5a51dae5281a4374421df5914c5b263
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663775"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

計算座標和地球上直線之間的最短距離。

**語法**

`geo_distance_point_to_line(`*經緯度*`, `*latitude*`, `*線串*`)`

**引數**

* *經度*:地理空間座標經度值(以度表示)。 有效值是實數,範圍為 [-180, [180]。
* *緯度*:地理空間座標緯度值(以度表示)。 有效值是實數,範圍為 [-90, [90]。
* *lineString*:在[GeoJSON 格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)數據類型中的行。

**傳回**

坐標與地球上一條線之間的最短距離(以米為單位)。 如果座標或行String 無效,則查詢將生成空結果。

> [!NOTE]
> * 地理空間座標被解釋為由[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系表示。
> * 測量地球距離的[大地測量基準是](https://en.wikipedia.org/wiki/Geodetic_datum)一個球體。 線邊是球體的測地線。

**線字串定義和約束**

動態("類型":"LineString","座標":[lng_1,lat_1],[lng_2,lat_2] ,..., [lng_N,lat_N] ])

* LineString 座標陣列必須至少包含兩個條目。
* 坐標 [經度, 緯度] 必須有效,其中經度是範圍 [-180, [180] 的實數,緯度是範圍 [-90, [90] 中的實數。
* 邊緣長度必須小於 180 度。 將選擇兩個頂點之間的最短邊。

> [!TIP]
> 為了更好的性能,請使用字面行。

**範例**

下面的示例查找北拉斯維加斯機場和附近道路之間的最短距離。

:::image type="content" source="images/queries/geo/distance_point_to_line.png" alt-text="北拉斯維加斯機場和道路之間的距離":::

```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

美國南部海岸的風暴事件。 事件由距離定義的岸線 5 km 的最大距離進行篩選。

:::image type="content" source="images/queries/geo/us_south_coast_storm_events.png" alt-text="美國南部海岸的風暴事件":::

```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

紐約計程車皮卡。 取件按與定義線路 0.1 m 的最大距離進行篩選。

:::image type="content" source="images/queries/geo/park_ave_ny_road.png" alt-text="美國南部海岸的風暴事件":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

以下範例將返回 null 結果,因為 LineString 輸入無效。
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

以下範例將傳回 null 結果,因為座標輸入無效。
```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |