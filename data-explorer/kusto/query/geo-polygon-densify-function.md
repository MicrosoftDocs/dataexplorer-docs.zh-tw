---
title: geo_polygon_densify （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_polygon_densify （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: bdb7bda617085ae1a7b3ead60c46c80c883943f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347777"
---
# <a name="geo_polygon_densify"></a>geo_polygon_densify()

藉由加入中繼點，將多邊形或 multipolygon 平面邊緣轉換為 geodesics。

## <a name="syntax"></a>語法

`geo_polygon_densify(`*多邊形* `, `*容錯*`)`

## <a name="arguments"></a>引數

* *多邊形*： [GeoJSON 格式](https://tools.ietf.org/html/rfc7946)的多邊形或 multipolygon，以及[動態](./scalar-data-types/dynamic.md)資料類型的。
* *容錯*：此為選擇性的數值，定義原始平面邊緣和轉換的量測計邊緣鏈之間的最大距離（以量為單位）。 支援的值位於 [0.1，10000] 範圍內。 如果未指定，則 `10` 會使用預設值。

## <a name="returns"></a>傳回

以[GeoJSON 格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的 Densified 多邊形。 如果多邊形或容錯無效，查詢將會產生 null 結果。

> [!NOTE]
> * 地理空間座標會以[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標參考系統來表示。
> * 必須正確定義多邊形，但函數不會檢查多邊形有效性。

**多邊形定義**

dynamic （{"type"： "多邊形"，"座標"： [LinearRingShell，LinearRingHole_1,..., LinearRingHole_N]}）

dynamic （{"type"： "MultiPolygon"，"座標"： [[LinearRingShell，LinearRingHole_1,..., LinearRingHole_N],..., [LinearRingShell，LinearRingHole_1,..., LinearRingHole_M]]}）

* LinearRingShell 是必要的，而且定義為 `counterclockwise` 座標 [[lng_1，lat_1],..., [lng_i，lat_i],..., [lng_j，lat_j],..., [lng_1，lat_1]] 的已排序陣列。 只能有一個 shell。
* LinearRingHole 是選擇性的，而且定義為已 `clockwise` 排序的座標陣列 [[lng_1，lat_1],..., [lng_i，lat_i],..., [lng_j，lat_j],..., [lng_1，lat_1]]。 可以有任意數目的內部環形和孔。
* LinearRing 頂點必須與至少三個座標不同。 第一個座標必須等於最後一個。 至少需要四個專案。
* 座標 [經度，緯度] 必須是有效的。 經度必須是範圍 [-180，+ 180] 中的實數，而緯度必須是範圍 [-90，+ 90] 中的實數。
* LinearRingShell 會包含在球體的一半。 LinearRing 會將球體劃分成兩個區域。 將選擇兩個區域中較小的一個。
* LinearRing 邊緣長度必須小於180度。 將選擇兩個頂點之間的最短邊緣。

**條件約束**

* Densified 多邊形中的最大點數數目限制為10485760。
* 以[動態](./scalar-data-types/dynamic.md)格式儲存多邊形具有大小限制。
* Densifying 有效的多邊形可能會使其失效。 此演算法會以非統一的方式新增點，因此可能會造成邊緣彼此 intertwine。

**動機**

* [GeoJSON 格式](https://tools.ietf.org/html/rfc7946)會定義兩個點之間的邊緣做為直笛線。
* 使用測測或平面邊緣的決策可能取決於資料集，而且在較長的邊緣特別相關。

## <a name="examples"></a>範例

下列範例會 densifies 曼哈頓中央公園多邊形。 邊緣很短，而平面邊緣與其測地線之間的距離小於容錯指定的距離。 因此，結果會保持不變。

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[-73.958244,40.800719],[-73.949146,40.79695],[-73.973093,40.764226],[-73.982062,40.768159],[-73.958244,40.800719]]]})))
```

|densified_polygon|
|---|
|{"type"： "多邊形"，"座標"： [[[-73.958244，40.800719]，[-73.949146，40.79695]，[-73.973093，40.764226]，[-73.982062，40.768159]，[-73.958244，40.800719]]]}|

下列範例會 densifies 多邊形的兩個邊緣。 Densified 邊緣長度為 ~ 110 公里

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]})))
```

|densified_polygon|
|---|
|{"type"： "多邊形"，"座標"： [[[10，10]，[10.25，10]，[10.5，10]，[10.75，10]，[11，10]，[11，11]，[10.75，11]，[10.5，11]，[10.25，11]，[10，11]，[10，10]]]}|

下列範例會傳回 null 結果，因為座標輸入無效。

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,900],[11,10],[11,11],[10,11],[10,10]]]}))
```

|densified_polygon|
|---|
||

下列範例會傳回 null 結果，因為有不正確容錯輸入。

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]}), 0)
```

|densified_polygon|
|---|
||
