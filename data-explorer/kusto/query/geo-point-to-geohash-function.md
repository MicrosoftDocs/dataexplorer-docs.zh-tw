---
title: geo_point_to_geohash （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_point_to_geohash （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c37789ac490814288c7331f0b1ae86b8b2178d67
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226919"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

計算地理位置的 geohash 字串值。

閱讀更多有關[geohash](https://en.wikipedia.org/wiki/Geohash)的資訊。  

**語法**

`geo_point_to_geohash(`*經度* `, `*緯度* `, `[*精確度*]`)`

**引數**

* *經度*：地理位置的經度值。 如果 x 是實數，而且在 [-180，+ 180] 範圍內，則經度 x 會視為有效。 
* *緯度*：地理位置的緯度值。 如果 y 是實數，而 y 的範圍是 [-90，+ 90]，則緯度 y 會視為有效。 
* *精確度*：定義所 `int` 要求精確度的選擇性。 支援的值位於 [1，18] 範圍內。 如果未指定，則 `5` 會使用預設值。

**傳回**

具有所要求精確度長度之指定地理位置的 geohash 字串值。 如果座標或精確度無效，查詢將會產生空的結果。

> [!NOTE]
>
> * Geohash 可以是有用的地理空間叢集工具。
> * Geohash 有18個精確度層級，區域涵蓋範圍從 25000000 km²最高層級1到0.6 μ²，最低層級為18。
> * Geohash 的常見前置詞表示彼此的鄰近點。 共用前置詞的時間愈長，兩個位置越接近。 精確度值會轉譯為 geohash 長度。
> * Geohash 是平面介面上的矩形區域。
> * 在以緯度 x 和緯度 y 計算的 geohash 字串上叫用[geo_geohash_to_central_point （）](geo-geohash-to-central-point-function.md)函數時，不一定會傳回 x 和 y。
> * 由於 geohash 定義，因此可能會有兩個地理位置非常接近彼此，但具有不同的 geohash 碼。

**Geohash 每個精確度值的矩形區域涵蓋範圍：**

| 精確度 | 寬度     | 高度    |
|----------|-----------|-----------|
| 1        | 5000公里   | 5000公里   |
| 2        | 1250公里   | 625公里    |
| 3        | 156.25 公里 | 156.25 公里 |
| 4        | 39.06 公里  | 19.53 公里  |
| 5        | 4.88 公里   | 4.88 公里   |
| 6        | 1.22 公里   | 0.61 公里   |
| 7        | 152.59 m  | 152.59 m  |
| 8        | 38.15 m   | 19.07 m   |
| 9        | 4.77 m    | 4.77 m    |
| 10       | 1.19 m    | 0.59 m    |
| 11       | 149.01 mm | 149.01 mm |
| 12       | 37.25 mm  | 18.63 mm  |
| 13       | 4.66 mm   | 4.66 mm   |
| 14       | 1.16 mm   | 0.58 mm   |
| 15       | 145.52 μ  | 145.52 μ  |
| 16       | 36.28 μ   | 18.19 μ   |
| 17       | 4.55 μ    | 4.55 μ    |
| 18       | 1.14 μ    | 0.57 μ    |

另請參閱[geo_point_to_s2cell （）](geo-point-to-s2cell-function.md)。

**範例**

Geohash 所匯總的美國風暴事件。

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="US geohash":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| geohash      |
|--------------|
| xn76m27ty9g4 |

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|geohash|
|---|
|dhwfz15h|

下列範例會尋找座標的群組。 群組中的每一對座標都位於4.88 公里乘以4.88 公里的矩形區域中。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", double(-122.303404), 47.570482,
  "B", double(-122.304745), 47.567052,
  "C", double(-122.278156), 47.566936,
]
| summarize count = count(),                                          // items per group count
            locations = make_list(location_id)                        // items in the group
            by geohash = geo_point_to_geohash(longitude, latitude)    // geohash of the group
```

| geohash | count | 位置  |
|---------|-------|------------|
| c23n8   | 2     | ["A"，"B"] |
| c23n9   | 1     | ["C"]      |

下列範例會產生空白的結果，因為座標輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| geohash |
|---------|
|         |

下列範例會產生空的結果，因為精確度輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| geohash |
|---------|
|         |
