---
title: parse_csv （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 parse_csv （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b61934ec2efbfb22c17fe93a4f3969a1592cefab
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780655"
---
# <a name="parse_csv"></a>parse_csv()

分割指定的字串，代表逗號分隔值的單一記錄，並傳回具有這些值的字串陣列。

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**語法**

`parse_csv(`*來源*`)`

**引數**

* *來源*：代表逗號分隔值之單一記錄的來源字串。

**傳回**

包含分割值的字串陣列。

**注意事項**

內嵌的換行文字、逗號和引號可以使用雙引號（' "'）來進行轉義。 此函式不支援每個資料列有多筆記錄（只會取得第一筆記錄）。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "aa"，<br>  "b，b，b"，<br>  "cc"、<br>  「轉義引號： \" 標題」 \" ，<br>  "line1\nline2"<br>]|

具有多筆記錄的 CSV 承載：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1"，<br>  "a"、<br>  "b"，<br>  "c"<br>]|
