---
title: 'geo_line_densify ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 geo_line_densify。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: f86ec0349b4e84215e9b2fdff33b2d705967bcac
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452811"
---
# <a name="geo_line_densify"></a>geo_line_densify()

藉由新增中繼點將平面線條或多行邊緣轉換成 geodesics。

## <a name="syntax"></a>Syntax

`geo_line_densify(`*lineString* `, `*容錯*`)`

## <a name="arguments"></a>引數

* *lineString*： [GeoJSON 格式](https://tools.ietf.org/html/rfc7946) 和 [動態](./scalar-data-types/dynamic.md) 資料類型的行或多行。
* *容錯*：選擇性數值，定義原始平面邊緣和已轉換之測計邊緣鏈之間的最大距離（以量測計）。 支援的值位於 [0.1，10000] 範圍內。 如果未指定，則 `10` 會使用預設值。

## <a name="returns"></a>傳回

[GeoJSON 格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的 Densified 行。 如果行或容錯無效，則查詢會產生 null 結果。

> [!NOTE]
> * 地理空間座標會以 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 座標參考系統的表示來解讀。

**LineString 定義**

dynamic ( {"type"： "LineString"，"座標"： [[lng_1，lat_1]，[lng_2，lat_2],..., [lng_N，lat_N]]} ) 

dynamic ( {"type"： "MultiLineString"，"座標"： [[line_1，line_2,..., line_N]]} ) 

* LineString 座標陣列必須包含至少兩個專案。
* 座標 [經度，緯度] 必須是有效的。 經度必須是範圍 [-180，+ 180] 內的實數，且緯度必須是範圍 [-90，+ 90] 的實數。
* Edge 長度必須小於180度。 將會選擇兩個頂點之間的最短邊緣。

**約束**

* Densified 行中的最大點數數目限制為10485760。
* 以 [動態](./scalar-data-types/dynamic.md) 格式儲存線條具有大小限制。

**動機**

* [GeoJSON 格式](https://tools.ietf.org/html/rfc7946) 會將兩個點之間的邊緣定義為直線笛卡兒線。
* 使用測測或平面邊緣的決策可能取決於資料集，且在長邊中特別相關。

## <a name="examples"></a>範例

下列範例會 densifies 曼哈頓島中的道路。 邊緣是簡短的，且平面邊緣與其量測兩者之間的距離小於容錯指定的距離。 因此，結果會維持不變。

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
|{"type"： "LineString"、"座標"： [[50，50]、[50.125，50.125]、[50.25，50.25]、[50.375，50.375]、[50.5、50.5]、[50.625、50.625]、[50.75、50.75]、[50.875、50.875]、[51，51]]}|

下列範例會傳回 null 結果，因為座標輸入無效。

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

下列範例會傳回 null 結果，因為容錯輸入無效。

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
