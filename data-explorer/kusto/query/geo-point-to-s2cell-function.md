---
title: geo_point_to_s2cell() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的geo_point_to_s2cell()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 253b850da519aceb0ead9456f7e49d6dd37d78ec
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663746"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

計算地理位置的 S2 單元格令牌字串值。

有關 S2 儲存格的詳細資訊,[請按下此處](http://s2geometry.io/devguide/s2cell_hierarchy)。

**語法**

`geo_point_to_s2cell(`*經緯度*`, `*latitude*`, `*水平*`)`

**引數**

* *經度*:地理位置的經度值。 如果 x 是實數,x 在 [-180, [180] 範圍內,則經度 x 將被視為有效。 
* *緯度*:地理位置的緯度值。 如果 y 是實際數位,則緯度 y 將被視為有效,並且 y 在範圍 [-90, [90] 中。 
* *級別*:`int`定義 請求的儲存格等級的可選。 支援的值在 [0,30] 範圍內。 如果未指定,則使用預設值`11`。

**傳回**

給定地理位置的 S2 單元格令牌字串值。 如果座標或級別無效,則查詢將生成空結果。

> [!NOTE]
>
> * S2Cell 是一個有用的地理空間聚類工具。
> * S2Cell 具有 31 級等級,面積覆蓋範圍從 85,011,012.19 平方公里到最高等級 0 到 00.44cm2 的最低級別 30。
> * S2Cell 在從 0 到 30 的水準提升期間很好地保存細胞中心。
> * S2Cell 是球體表面上的一個單元格,其邊緣是測地線。
> * 在 s2cell 令牌字串上調用[geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md)函數,該函數是在經度 x 和緯度 y 上計算的,不一定返回 x 和 y。
> * 兩個地理位置可能彼此非常接近,但具有不同的 S2 Cell 令牌。

**S2 每個等級值的儲存格近似區域覆寫範圍**

對於每個級別,s2cell 的大小相似,但不完全相等。 附近的細胞大小往往更相等。

|層級|最小隨機細胞邊緣長度(英國)|最大隨機儲存格邊緣長度(美國)|
|--|--|--|
|0|7842 公里|7842 公里|
|1|3921 公里|5004 公里|
|2|1825 公里|2489 公里|
|3|840 公里|1310 公里|
|4|432 公里|636 公里|
|5|210 公里|315 公里|
|6|108 公里|156 公里|
|7|54 公里|78 公里|
|8|27 公里|39 公里|
|9|14 公里|20 公里|
|10|7 公里|10 公里|
|11|3 公里|5 公里|
|12|1699米|2 公里|
|13|850米|1225 公尺|
|14|425 公尺|613 公尺|
|15|212 公尺|306 公尺|
|16|106 公尺|153 公尺|
|17|53 公尺|77 公尺|
|18|27 公尺|38 公尺|
|19|13 公尺|19 公尺|
|20|7 公尺|10 公尺|
|21|3 公尺|5 公尺|
|22|166 釐米|2 公尺|
|23|83 公分|120 釐米|
|24|41 釐米|60 釐米|
|25|21 釐米|30 釐米|
|26|10 釐米|15 釐米|
|27|5 釐米|7 公分|
|28|2 公分|4 公分|
|29|12 mm|18mm|
|30|6 釐米|9 釐米|

表源可以[在這裡](http://s2geometry.io/resources/s2cell_statistics)找到。

另請參閱[geo_point_to_geohash()](geo-point-to-geohash-function.md)。

**範例**

美國風暴事件由s2cell聚合。

:::image type="content" source="images/queries/geo/s2cell.png" alt-text="美國 s2cell":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

下面的示例查找座標組。 組中的每對座標都位於 s2cell 中,最大面積為 1632.45 平方公里。
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", 10.1234, 53,
  "B", 10.3579, 53,
  "C", 10.6842, 53,
]
| summarize count = count(),                                        // items per group count
            locations = make_list(location_id)                      // items in the group
            by s2cell = geo_point_to_s2cell(longitude, latitude, 8) // s2 cell of the group
```

| s2cell | count | 位置 |
|--------|-------|-----------|
| 47b1d  | 2     | ["A","B"] |
| 47ae3  | 1     | ["C"]     |

以下示例由於座標輸入無效而生成空結果。
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

以下示例由於水平輸入無效而生成空結果。
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

以下示例由於水平輸入無效而生成空結果。
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |