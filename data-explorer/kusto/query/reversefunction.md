---
title: 反向() - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 數據資源管理器中介紹反向()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e95246e0586dff7dd89dc2658c7fae08b1bbaddf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510297"
---
# <a name="reverse"></a>reverse()

函數使輸入字串反轉。

如果輸入值不是字串類型,則函數強制將該值強制轉換為字串。

**語法**

`reverse(`*源*`)`

**引數**

* *源*:輸入值。  

**傳回**

字串值的反向順序。

**範例**

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|字串|rstr|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVTRPONKJIJFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|時間|rtimespan|
|---|---|---|---|
|54321|54.321|Z0000000.00:00:21T51-01-7102|00:00:30|




 