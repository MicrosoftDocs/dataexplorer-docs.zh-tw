---
title: 'trim_start ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 trim_start。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6f4341e984504c89bfc4d5a1265c5193ac6d0297
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251887"
---
# <a name="trim_start"></a>trim_start()

移除指定之正則運算式的前置比對。

## <a name="syntax"></a>語法

`trim_start(`*RegEx* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：要從*文字*開頭修剪的字串或[正則運算式](re2.md)。  
* *text*：字串。

## <a name="returns"></a>傳回

在*文字*開頭找到的*RegEx 符合 RegEx*之後的*文字*。

## <a name="example"></a>範例

語句下方從*string_to_trim*的開頭修剪*子字串*：

```kusto
let string_to_trim = @"https://bing.com";
let substring = "https://";
print string_to_trim = string_to_trim,trimmed_string = trim_start(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|https://bing.com|bing.com|

下一個語句會修剪字串開頭的所有非文字字元：

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_start(@"[^\w]+",str)
```

|字串|trimmed_str|
|---|---|
|-Te st1//$|Te st1//$|
|-Te st2//$|Te st2//$|
|-Te st3//$|Te st3//$|
|-Te st4//$|Te st4//$|
|-Te st5//$|Te st5//$|

 