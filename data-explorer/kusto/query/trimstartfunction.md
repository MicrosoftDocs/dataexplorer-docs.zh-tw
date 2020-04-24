---
title: trim_start() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的trim_start()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1d4ae71f73e76005f89766d974192c8eb24cd74d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505571"
---
# <a name="trim_start"></a>trim_start()

刪除指定正規表示式的前導匹配項。

**語法**

`trim_start(`*正規文字*`,`*text*`)`

**引數**

* *正規表示式*: 要從*文字*開頭修剪的字串或[正規表示式](re2.md)。  
* *文字*:字串。

**傳回**

*在**文字*開頭找到*的 regex*修剪比的文字。

**範例**

語句波紋管從*string_to_trim*開始修剪*子字串*:

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

下一個語句從字串開頭修剪所有非單字元:

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|字串|trimmed_str|
|---|---|
|- Te st1// $|Te st1// $|
|- Te st2// $|Te st2// $|
|- Te st3// $|Te st3// $|
|- Te st4// $|Te st4// $|
|- Te st5// $|Te st5// $|

 