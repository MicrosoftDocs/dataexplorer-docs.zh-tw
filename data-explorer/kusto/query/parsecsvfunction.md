---
title: parse_csv() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的parse_csv()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6645fd0dcc7062a4afb60fb34ade1c519af27294
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663679"
---
# <a name="parse_csv"></a>parse_csv()

拆分表示逗號分隔值單個記錄的給定字串,並返回具有這些值的字串元組。

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**語法**

`parse_csv(`*源*`)`

**引數**

* *源*:表示逗號分隔值的單個記錄的原始字串。

**傳回**

包含分割值的字串陣列。

**注意事項**

可以使用雙引號 ('')轉義嵌入的行源、逗號和引號。 此函數不支援每行的多個記錄(僅獲取第一條記錄)。

**範例**

```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "aa",<br>  "b,b,b",<br>  "cc",<br>  "轉義報價:\"\"標題 "<br>  "行1\nline2"<br>]|

具有多個紀錄的 CSV 有效負載:

```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "記錄1"<br>  "a",<br>  "b",<br>  "c"<br>]|