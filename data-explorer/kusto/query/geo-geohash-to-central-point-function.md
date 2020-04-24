---
title: geo_geohash_to_central_point() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的geo_geohash_to_central_point()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a71265b58cffcec2fcf9d6ac8d412c8dd44866f4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514632"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

計算表示地理哈希矩形區域中心的地理空間座標。

有關 Geohash 的更多資訊,請參閱[維琪百科](https://en.wikipedia.org/wiki/Geohash)。  

**語法**

`geo_geohash_to_central_point(`*地哈希*`)`

**引數**

*地理哈希*:地理哈希字串值,因為它是由[geo_point_to_geohash()](geo-point-to-geohash-function.md)計算的。 地理哈希字串可以是 1 到 18 個字元。

**傳回**

[地理JSON格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的地理空間座標值。 如果地理哈希無效,則查詢將生成空結果。

> [!NOTE]
> GeoJSON 格式指定經度第一和緯度秒。

**範例**

```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|點|座標|經度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "類型":"點",<br>  "座標": |<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

以下範例返回 null 結果,因為地理哈希輸入無效。

```kusto
print geohash = geo_geohash_to_central_point("a")
```

|地哈希|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>範例:為必應地圖建立位置深層連結

您可以使用 Geohash 值透過指向 Geohash 中心點來建立到必應地圖的深層連結 URL:

```kusto
// Use string concatenation to create Bing Map deep-link URL from a geo-point
let point_to_map_url = (_point:dynamic, _title:string) 
{
    strcat('https://www.bing.com/maps?sp=point.', _point.coordinates[1] ,'_', _point.coordinates[0], '_', url_encode(_title)) 
};
// Convert geohash to center point, and then use 'point_to_map_url' to create Bing Map deep-link
let geohash_to_map_url = (_geohash:string, _title:string)
{
    point_to_map_url(geo_geohash_to_central_point(_geohash), _title)
};
print geohash = 'sv8wzvy7'
| extend url = geohash_to_map_url(geohash, "You are here")
```

|地哈希|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|