---
title: 反向（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的反向（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22fe505eb8fd391e7a61120dbf42c214cb61c120
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264822"
---
# <a name="reverse"></a>reverse()

函式會反轉輸入字串的順序。
如果輸入值的類型不是，則函式會 `string` 強制將值轉換為類型 `string` 。

**語法**

`reverse(`*來源*`)`

**引數**

* *來源*：輸入值。  

**傳回**

字串值的反向順序。

**範例**

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|字串|rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVUTSRQPONMLKJIHGFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54.321|Z 0000000.00：00： 21T51-01-7102|00:00:30|
