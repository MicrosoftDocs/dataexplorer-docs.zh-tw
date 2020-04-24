---
title: geo_s2cell_to_central_point() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 geo_s2cell_to_central_point()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 624d163b508768d0a5316ee3ec62a12b11942176
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514428"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

計算表示 S2 單元格中心的地理空間座標。

有關 S2 儲存格的詳細資訊,[請按下此處](http://s2geometry.io/devguide/s2cell_hierarchy)。

**語法**

`geo_s2cell_to_central_point(`*s2cell*`)`

**引數**

*s2cell*: S2 單元格令牌字串值,因為它是由[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)計算的。 S2 Cell 權杖的最大字串長度為 16 個字元。

**傳回**

[地理JSON格式](https://tools.ietf.org/html/rfc7946)和[動態](./scalar-data-types/dynamic.md)資料類型的地理空間座標值。 如果 S2 單元格令牌無效,則查詢將生成空結果。

> [!NOTE]
> GeoJSON 格式指定經度第一和緯度秒。

**範例**

```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|點|座標|經度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "類型":"點",<br>  "座標": |<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

以下範例返回 null 結果,因為 S2 單元格令牌輸入無效。
```kusto
print point = geo_s2cell_to_central_point("a")
```

|點|
|---|
||