---
title: geo_line_densify （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_line_densify （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: c5a66255f719d3bd0da962a8eb9d3cae23a8c254
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347828"
---
# <a name="geo_line_densify"></a>geo_line_densify()

藉由加入中繼點，將平面線條邊緣轉換成 geodesics。

## <a name="syntax"></a>語法

`geo_line_densify(`*lineString* `, `*容錯*`)`

## <a name="arguments"></a>引數

* *lineString*： [GeoJSON 格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的行。
* *容錯*：此為選擇性的數值，定義原始平面邊緣和轉換的量測計邊緣鏈之間的最大距離（以量為單位）。 支援的值位於 [0.1，10000] 範圍內。 如果未指定，則 `10` 會使用預設值。

## <a name="returns"></a>傳回

[GeoJSON 格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的 Densified 行。 如果行或容錯無效，查詢將會產生 null 結果。

> [!NOTE]
> * 地理空間座標會以[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系統來表示。

**LineString 定義**

dynamic （{"type"： "LineString"，"座標"： [[lng_1，lat_1]，[lng_2，lat_2],..., [lng_N，lat_N]]}）

* LineString 座標陣列必須包含至少兩個專案。
* 座標 [經度，緯度] 必須是有效的。 經度必須是範圍 [-180，+ 180] 中的實數，而緯度必須是範圍 [-90，+ 90] 中的實數。
* 邊緣長度必須小於180度。 將選擇兩個頂點之間的最短邊緣。

**條件約束**

* Densified 行中的最大點數數目限制為10485760。
* 以[動態](./scalar-data-types/dynamic.md)格式儲存線條具有大小限制。

**動機**

* [GeoJSON 格式](https://tools.ietf.org/html/rfc7946)會定義兩個點之間的邊緣做為直笛線。
* 使用測測或平面邊緣的決策可能取決於資料集，而且在較長的邊緣特別相關。

## <a name="examples"></a>範例

下列範例會 densifies 曼哈頓島中的道路。 邊緣是 short，而平面邊緣與其測地線之間的距離小於容錯指定的距離。 因此，結果會保持不變。

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[-73.949247, 40.796860],[-73.973017, 40.764323]]})))
```

|densified_line|
|---|
|{"type"： "LineString"，"座標"： [[-73.949247，40.796860]，[-73.973017，40.764323]]}|

下列範例會 densifies ~ 130km length 的邊緣

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[50, 50], [51, 51]]})))
```

|densified_line|
|---|
|{"type"： "LineString"、"座標"： [[50，50]、[50.125，50.125]、[50.25、50.25]、[50.375、50.375]、[50.5、50.5]、[50.625、50.625]、[50.75、50.75]、[50.875，50.875]、[51，51]]}|

下列範例會傳回 null 結果，因為座標輸入無效。

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

下列範例會傳回 null 結果，因為有不正確容錯輸入。

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
