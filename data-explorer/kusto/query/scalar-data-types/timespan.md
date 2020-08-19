---
title: Timespan 資料類型-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 timespan 資料類型。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 204076e8ed079dec69cae7080e7d2c50df52a9a6
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610316"
---
# <a name="the-timespan-data-type"></a>Timespan 資料類型

`timespan` (`time`) 資料類型代表時間間隔。

## <a name="timespan-literals"></a>timespan 常值

型別的常 `timespan` 值具有語法 `timespan(` *值* `)` ，其中支援*值*的格式數，如下表所示：

|值|時間長度|
---|---
`2d`|2 天
`1.5h`|1.5 小時
`30m`|30 分鐘
`10s`|10 秒
`0.1s`|0.1 秒
`100ms`| 100 毫秒
`10microsecond`|10毫秒
`1tick`|100ns
`time(15 seconds)`|15 秒
`time(2)`| 2 天
`time(0.12:34:56.7)`|`0d+12h+34m+56.7s`

特殊形式 `time(null)` 為 [null 值](null-values.md)。

## <a name="timespan-operators"></a>timespan 運算子

您 `timespan` 可以新增、減去和除以型別的兩個值。
最後一個作業會傳回型別值， `real` 代表一個值可以容納另一個值的小數位數。

## <a name="examples"></a>範例

下列範例會以數種方式計算一天中的秒數：

```kusto
print
    result1 = 1d / 1s,
    result2 = time(1d) / time(1s),
    result3 = 24 * 60 * time(00:01:00) / time(1s)
```