---
title: trim_start （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 trim_start （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4550fb07da37658ecf11a4eb04ecdf199d8ba989
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339539"
---
# <a name="trim_start"></a>trim_start()

移除指定之正則運算式的前置比對。

## <a name="syntax"></a>語法

`trim_start(`*RegEx* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：要從*文字*開頭修剪的字串或[正則運算式](re2.md)。  
* *text*：字串。

## <a name="returns"></a>傳回

在*文字*開頭找到的*RegEx*相符後的*文字*。

## <a name="example"></a>範例

語句鈴會從*string_to_trim*開始修剪*子字串*：

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

 