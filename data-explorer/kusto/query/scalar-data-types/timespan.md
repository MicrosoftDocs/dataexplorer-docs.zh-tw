---
title: 時間跨度資料類型 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的時間跨度數據類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31a0bfafed817ffaf531cffdcb844da8a357531f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509600"
---
# <a name="the-timespan-data-type"></a>時間跨度資料類型

`timespan` (`time`) 資料類型表示時間間隔。

## <a name="timespan-literals"></a>時間跨度文字

型`timespan`態文字具有語`timespan(`法*值*`)`,其中支援許多*格式的值,* 如下表所示:

|||
---|---
`2d`|2 天
`1.5h`|1.5 小時
`30m`|30 分鐘
`10s`|10 秒
`0.1s`|0.1 秒
`100ms`| 100 毫秒
`10microsecond`|10 微秒
`1tick`|100ns
`time(15 seconds)`|15 秒
`time(2)`| 2 天
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

特殊形式`time(null)`為[null 值](null-values.md)。

## <a name="timespan-operators"></a>時間跨度運算子

可以添加、減去`timespan`和劃分兩個類型值。
最後一個操作返回一個類型`real`值,表示一個值可以容納另一個值的小數。

## <a name="examples"></a>範例

下面的範例以多種方式計算一天中的秒數:

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```