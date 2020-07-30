---
title: trim_end （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 trim_end （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cab78680a3b996234724bc052d75959928520289
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339845"
---
# <a name="trim_end"></a>trim_end()

移除指定之正則運算式的尾端比對。

## <a name="syntax"></a>語法

`trim_end(`*RegEx* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：要從*文字*結尾修剪的字串或[正則運算式](re2.md)。  
* *text*：字串。

## <a name="returns"></a>傳回

修剪在*文字*結尾處找到的*RegEx*相符專案之後的*文字*。

## <a name="example"></a>範例

語句鈴會從*string_to_trim*的結尾修剪*子字串*：

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |Bing          |

下一個語句會修剪字串結尾的所有非文字字元：

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