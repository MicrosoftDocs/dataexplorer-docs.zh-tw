---
title: geo_geohash_to_central_point （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_geohash_to_central_point （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: eb59eae0bc014c6ce9060d65f6c3aced80e4275c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227123"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

計算代表 geohash 矩形區域中心的地理空間座標。

閱讀更多相關資訊 [`geohash`](https://en.wikipedia.org/wiki/Geohash) 。  

**語法**

`geo_geohash_to_central_point(`*geohash*`)`

**引數**

*geohash*： geohash 字串值，因為它是由[geo_point_to_geohash （）](geo-point-to-geohash-function.md)所計算。 Geohash 字串可以是1到18個字元。

**傳回**

地理空間座標值為[GeoJSON 格式](https://tools.ietf.org/html/rfc7946)，而[動態](./scalar-data-types/dynamic.md)資料類型為。 如果 geohash 無效，查詢將會產生 null 結果。

> [!NOTE]
> GeoJSON 格式會指定經度 first 和緯度 second。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|點|座標|經度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "type"： "Point"，<br>  「座標」： [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

下列範例會傳回 null 結果，因為不正確 geohash 輸入。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_geohash_to_central_point("a")
```

|geohash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>範例：為 Bing 地圖服務建立位置深層連結

您可以使用 geohash 值，透過指向 geohash 中心點來建立 Bing 地圖的深層連結 URL：

<!-- csl: https://help.kusto.windows.net/Samples -->
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

|geohash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|
