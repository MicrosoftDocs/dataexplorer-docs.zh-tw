---
title: 'trim_end ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 trim_end。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6a764752f126408ceb48f1c4a1af5c74014b6eab
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251973"
---
# <a name="trim_end"></a>trim_end()

移除指定之正則運算式的尾端相符項。

## <a name="syntax"></a>語法

`trim_end(`*RegEx* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：要從*文字*結尾修剪的字串或[正則運算式](re2.md)。  
* *text*：字串。

## <a name="returns"></a>傳回

在*文字*結尾找到的*RegEx 符合 RegEx*之後的*文字*。

## <a name="example"></a>範例

語句下方從*string_to_trim*的結尾修剪*子字串*：

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |Bing          |

下一個語句會從字串的結尾修剪所有非文字字元：

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|字串          |trimmed_str|
|-------------|-----------|
|-Te st1//$|-Te st1  |
|-Te st2//$|-Te st2  |
|-Te st3//$|-Te st3  |
|-Te st4//$|-Te st4  |
|-Te st5//$|-Te st5  |