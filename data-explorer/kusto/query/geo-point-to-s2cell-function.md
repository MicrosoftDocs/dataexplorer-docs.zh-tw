---
title: geo_point_to_s2cell （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_point_to_s2cell （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a094644e6f8a96631e5e1bcf1d2d15cf1ba7caad
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347743"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

計算地理位置的 S2 資料格權杖字串值。

閱讀更多有關[S2 資料格](https://s2geometry.io/devguide/s2cell_hierarchy)階層的資訊。

## <a name="syntax"></a>語法

`geo_point_to_s2cell(`*經度* `, `*緯度* `, `*層級*`)`

## <a name="arguments"></a>引數

* *經度*：地理位置的經度值。 如果*x*是實數，而*x*是在 [-180，+ 180] 範圍內，則經度*x*會視為有效。 
* *緯度*：地理位置的緯度值。 如果 y 是實數，而 y 在範圍 [-90，+ 90] 中，則緯度 y 會視為有效。 
* *level*： `int` 定義要求的資料格層級的選擇性。 支援的值位於 [0，30] 範圍內。 如果未指定，則 `11` 會使用預設值。

## <a name="returns"></a>傳回

給定地理位置的 S2 資料格標記字串值。 如果座標或層級無效，查詢將會產生空的結果。

> [!NOTE]
>
> * S2 資料格可以是有用的地理空間叢集工具。
> * S2 資料格的階層層級為31個，範圍涵蓋範圍從85011，012.19 公里²在最高層級0，00.44 cm ²最低層級30。
> * S2 資料格會在層級從0到30的增加期間，保留資料格中心。
> * S2 資料格是球形表面上的資料格，而其邊緣則是 geodesics。
> * 在以緯度 x 和緯度 y 計算的 S2 資料格標記字串上叫用[geo_s2cell_to_central_point （）](geo-s2cell-to-central-point-function.md)函數，不一定會傳回 x 和 y。
> * 兩個地理位置可能非常接近彼此，但有不同的 S2 資料格權杖。

**S2 資料格每層級值的大約區域涵蓋範圍**

針對每個層級，S2 資料格的大小類似，但不完全相同。 附近的資料格大小通常比較大。

|層級|最小隨機儲存格邊緣長度（英國）|最大隨機儲存格邊緣長度（US）|
|--|--|--|
|0|7842公里|7842公里|
|1|3921公里|5004公里|
|2|1825公里|2489公里|
|3|840公里|1310公里|
|4|432公里|636公里|
|5|210公里|315公里|
|6|108公里|156公里|
|7|54公里|78公里|
|8|27公里|39公里|
|9|14公里|20公里|
|10|7公里|10公里|
|11|3公里|5公里|
|12|1699 m|2公里|
|13|850 m|1225 m|
|14|425 m|613 m|
|15|212 m|306 m|
|16|106 m|153 m|
|17|53 m|77 m|
|18|27 m|38 m|
|19|13 m|19 m|
|20|7 m|10 m|
|21|3 m|5 m|
|22|166 cm|2 m|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30 cm|
|26|10 cm|15 cm|
|27|5 cm|7 cm|
|28|2 cm|4 cm|
|29|12 mm|18 mm|
|30|6 mm|9 mm|

您可以[在此 S2 資料格統計資源中](https://s2geometry.io/resources/s2cell_statistics)找到資料表來源。

另請參閱[geo_point_to_geohash （）](geo-point-to-geohash-function.md)。

## <a name="examples"></a>範例

S2cell 所匯總的美國風暴事件。

:::image type="content" source="images/geo-point-to-s2cell-function/s2cell.png" alt-text="US s2cell":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

下列範例會尋找座標的群組。 群組中的每一對座標都位於 S2 資料格中，且最大區域為 1632.45 km²。

<!-- csl: https://help.kusto.windows.net/Samples -->
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
| 47b1d  | 2     | ["A"，"B"] |
| 47ae3  | 1     | ["C"]     |

下列範例會產生空白的結果，因為座標輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

下列範例會產生空的結果，因為層級輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

下列範例會產生空的結果，因為層級輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |
