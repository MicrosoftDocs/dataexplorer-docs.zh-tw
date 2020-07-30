---
title: replace （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 replace （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 47e2724e76abde2133c075d9270783fa64ae73bb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345805"
---
# <a name="replace"></a>replace()

用另一個字串取代所有 regex 相符項目。

## <a name="syntax"></a>語法

`replace(`*RegEx* `,`*重寫* `,`*文字*`)`

## <a name="arguments"></a>引數

* *RegEx*：用來搜尋*文字*的[正則運算式](https://github.com/google/re2/wiki/Syntax)。 它可以在 '('括號')' 中包含擷取群組。 
* *重寫*： *matchingRegEx 找到*所做之任何比對的取代 RegEx。 使用 `\0` 來代表整個相符項目、`\1` 來代表第一個擷取群組，`\2` 和以上的數字來代表後續的擷取群組。
* *text*：字串。

## <a name="returns"></a>傳回

以 rewrite** 的評估取代 regex** 的所有相符項目後的 text**。 相符項目不會重疊。

## <a name="example"></a>範例

此陳述式︰

```kusto
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

具有下列結果︰

| x    | 字串 | 取代後|
|---|---|---|
| 1    | Number is 1.000000  | Number was: 1.000000|
| 2    | Number is 2.000000  | Number was: 2.000000|
| 3    | Number is 3.000000  | Number was: 3.000000|
| 4    | Number is 4.000000  | Number was: 4.000000|
| 5    | Number is 5.000000  | Number was: 5.000000|
 