---
title: 'trim ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 trim ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a5a8bf4884bf6c493c1b3b960fce64fe143ed52e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251899"
---
# <a name="trim"></a>trim()

移除指定之正則運算式的所有開頭和尾端相符專案。

## <a name="syntax"></a>語法

`trim(`*RegEx* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：要從*文字*開頭和/或結尾修剪的字串或[正則運算式](re2.md)。  
* *text*：字串。

## <a name="returns"></a>傳回

修剪後的*文字*會在*文字*開頭和/或結尾找到的*RegEx*相符。

## <a name="example"></a>範例

語句下方從*string_to_trim*的開頭和結尾修剪*子字串*：

```kusto
let string_to_trim = @"--https://bing.com--";
let substring = "--";
print string_to_trim = string_to_trim, trimmed_string = trim(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|---|---|
|--https://bing.com--|https://bing.com|

下一個語句會修剪字串開頭和結尾的所有非文字字元：

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


 