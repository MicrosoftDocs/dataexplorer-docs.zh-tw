---
title: geo_point_to_geohash() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的geo_point_to_geohash()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: b69d22c56cef4b54a99aa9aa3e9897a2ef177834
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030103"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

計算地理位置的 Geohash 字串值。

有關 Geohas 的資訊,請參閱[此處](https://en.wikipedia.org/wiki/Geohash)。  

**語法**

`geo_point_to_geohash(`*經緯度*`, `*latitude*`, `[*精度*]`)`

**引數**

* *經度*:地理位置的經度值。 如果 x 是實數且範圍在 [-180, [180] 範圍內,則經度 x 將被視為有效。 
* *緯度*:地理位置的緯度值。 如果 y 是實數,並且 y 在 [-90, [90] 範圍內,緯度 y 將被視為有效。 
* *精度*:`int`定義 請求的準確性的可選。 支援的值在 [1,18] 範圍內。 如果未指定,則使用預設值`5`。

**傳回**

給定地理位置的 Geohash 字串值,具有請求的精度長度。 如果座標或精度無效,則查詢將生成空結果。

> [!NOTE]
>
> * 地理哈希可以是一個有用的地理空間聚類工具。
> * Geohash 具有 18 個精度級別,其區域覆蓋率從最高等級 1 到 18 的 0.6 μ2 的 2500 萬平方公里到 0.6 μ2 不等。
> * Geohass 的常見首碼指示點彼此的接近。 共用首碼的時間越長,兩個位置越近。 精度值轉換為地理哈希長度。
> * 地理哈希是平面上的矩形區域。
> * 在基於經度 x 和緯度 y 計算的地理哈希字串上調用[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)函數不一定傳回 x 和 y。
> * 由於 Geohash 定義,兩個地理位置可能彼此非常接近,但具有不同的地理哈希代碼。

**每個精度值的地理哈希矩形面積覆蓋率:**

| 精確度 | 寬度     | 高度    |
|----------|-----------|-----------|
| 1        | 5000 公里   | 5000 公里   |
| 2        | 1250 公里   | 625 公里    |
| 3        | 156.25 公里 | 156.25 公里 |
| 4        | 39.06 公里  | 19.53 公里  |
| 5        | 4.88 公里   | 4.88 公里   |
| 6        | 1.22 公里   | 0.61 公里   |
| 7        | 152.59 公尺  | 152.59 公尺  |
| 8        | 38.15 公尺   | 19.07 公尺   |
| 9        | 4.77 公尺    | 4.77 公尺    |
| 10       | 1.19 公尺    | 0.59 公尺    |
| 11       | 149.01 mm | 149.01 mm |
| 12       | 37.25 mm  | 18.63 mm  |
| 13       | 4.66 mm   | 4.66 mm   |
| 14       | 1.16 mm   | 0.58 mm   |
| 15       | 145.52 |  | 145.52 |  |
| 16       | 36.28 |   | 18.19 |   |
| 17       | 4.55 |    | 4.55 |    |
| 18       | 1.14 |    | 0.57 |    |

另請參閱[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)。

**範例**

美國風暴事件由地理哈希聚合。

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="美國地質哈希":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| 地哈希      |
|--------------|
| xn76m27ty9g4 |

```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|地哈希|
|---|
|德夫茲15h|

下面的示例查找座標組。 小組中的每對座標都位於4.88公里的長方形區域內,4.88公里。
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

| 地哈希 | count | 位置  |
|---------|-------|------------|
| c23n8   | 2     | ["A","B"] |
| c23n9   | 1     | ["C"]      |

以下示例由於座標輸入無效而生成空結果。
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| 地哈希 |
|---------|
|         |

以下示例由於精度輸入無效而生成空結果。
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| 地哈希 |
|---------|
|         |