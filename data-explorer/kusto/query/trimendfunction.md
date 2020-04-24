---
title: trim_end() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的trim_end()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a6f6ffc264cb436fc61d74f08dfded915caa05d4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505639"
---
# <a name="trim_end"></a>trim_end()

刪除指定正規表示式的尾隨匹配項。

**語法**

`trim_end(`*正規文字*`,`*text*`)`

**引數**

* *正規表示式*: 要從*文字*末尾修剪的字串或[正規表示式](re2.md)。  
* *文字*:字串。

**傳回**

*在**文字*末尾找到*的 regex*的修整匹配後的文本。

**範例**

語句波紋管從*string_to_trim*末尾修剪*子字串*:

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |Bing          |

下一個語句從字串末尾修剪所有非單字元:

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|字串          |trimmed_str|
|-------------|-----------|
|- Te st1// $|- Te st1  |
|- Te st2// $|- Te st2  |
|- Te st3// $|- Te st3  |
|- Te st4// $|- Te st4  |
|- Te st5// $|- Te st5  |