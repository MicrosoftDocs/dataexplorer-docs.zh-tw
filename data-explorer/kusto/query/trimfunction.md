---
title: trim （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 trim （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a28ca267612bef68c676118331b3010a8c947e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350633"
---
# <a name="trim"></a>trim()

移除指定之正則運算式的所有開頭和結尾相符專案。

## <a name="syntax"></a>語法

`trim(`*RegEx* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：要從開頭和/或*文字*結尾修剪的字串或[正則運算式](re2.md)。  
* *text*：字串。

## <a name="returns"></a>傳回

修剪比對開始和/或*文字*結尾處找到的*RegEx*相符專案之後的*文字*。

## <a name="example"></a>範例

語句鈴會從*string_to_trim*的開頭和結尾修剪*子字串*：

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

下一個語句會從字串的開頭和結尾修剪所有非文字字元：

```kusto
range x from 1 to 5 step 1
| project str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim(@"[^\w]+",str)
```

|字串|trimmed_str|
|---|---|
|-Te st1//$|Te st1|
|-Te st2//$|Te st2|
|-Te st3//$|Te st3|
|-Te st4//$|Te st4|
|-Te st5//$|Te st5|


 