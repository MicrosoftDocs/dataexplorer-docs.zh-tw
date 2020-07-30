---
title: geo_s2cell_to_central_point （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 geo_s2cell_to_central_point （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 30075b5a75e273061423a6f1540f44947ef93cec
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347709"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

計算代表 S2 資料格中心的地理空間座標。

閱讀更多有關[S2 資料格](https://s2geometry.io/devguide/s2cell_hierarchy)階層的資訊。

## <a name="syntax"></a>語法

`geo_s2cell_to_central_point(`*s2cell*`)`

## <a name="arguments"></a>引數

*s2cell*： S2 資料格權杖字串值，因為它是由[geo_point_to_s2cell （）](geo-point-to-s2cell-function.md)所計算。 S2 資料格權杖的最大字串長度為16個字元。

## <a name="returns"></a>傳回

地理空間座標值為[GeoJSON 格式](https://tools.ietf.org/html/rfc7946)，而[動態](./scalar-data-types/dynamic.md)資料類型為。 如果 S2 資料格 token 無效，查詢將會產生 null 結果。

> [!NOTE]
> GeoJSON 格式會指定經度 first 和緯度 second。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|點|座標|經度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "type"： "Point"，<br>  「座標」： [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

下列範例會傳回 null 結果，因為 S2 資料格權杖輸入無效。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("a")
```

|點|
|---|
||
