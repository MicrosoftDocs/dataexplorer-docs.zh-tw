---
title: '反向 ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的反向 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dc348643ff6e098b2291e69d68ca817c1f5af9c2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243008"
---
# <a name="reverse"></a>reverse()

函數會反轉輸入字串的順序。
如果輸入值的類型不是，則函式會 `string` 強制將值轉換為類型 `string` 。

## <a name="syntax"></a>語法

`reverse(`*源*`)`

## <a name="arguments"></a>引數

* *來源*：輸入值。  

## <a name="returns"></a>傳回

字串值的反轉順序。

## <a name="examples"></a>範例

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|字串|rstr|
|---|---|
|>ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVUTSRQPONMLKJIHGFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54.321|Z 0000000.00：00： 21T51-01-7102|00:00:30|
