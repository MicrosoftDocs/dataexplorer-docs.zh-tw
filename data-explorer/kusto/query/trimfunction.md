---
title: 修剪() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 trim()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: aef2a61e5ac13fe9af9d8bc0dd130f3d085a3604
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505588"
---
# <a name="trim"></a>trim()

刪除指定正規表示式的所有前導和尾隨匹配項。

**語法**

`trim(`*正規文字*`,`*text*`)`

**引數**

* *正規表示式*: 要從*文字*的開頭與/或結尾修剪的字串或[正規表示式](re2.md)。  
* *文字*:字串。

**傳回**

*在**文字*的開頭和/或末尾找到*的 regex*的修剪匹配後的文字。

**範例**

語句波紋管從*string_to_trim*的開頭與結尾修剪*子字串*:

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

下一個語句從字串的開頭和結尾修剪所有非單字元:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|字串|trimmed_str|
|---|---|
|- Te st1// $|Te st1|
|- Te st2// $|Te st2|
|- Te st3// $|Te st3|
|- Te st4// $|Te st4|
|- Te st5// $|Te st5|


 