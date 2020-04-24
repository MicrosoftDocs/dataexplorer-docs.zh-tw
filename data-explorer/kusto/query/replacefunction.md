---
title: 取代() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的替換()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84a741f10172ef418da7d92b8c1ad6ba26593d72
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510331"
---
# <a name="replace"></a>replace()

用另一個字串取代所有 regex 相符項目。

**語法**

`replace(`*正則重寫*`,`*rewrite*`,`*文字*`)`

**引數**

* *正規表示式*:用於搜尋*文字*的[正規表示式](https://github.com/google/re2/wiki/Syntax)。 它可以在 '('括號')' 中包含擷取群組。 
* *重寫*: 任何*符合 Regex*進行的任何匹配的替換正則運算式。 使用 `\0` 來代表整個相符項目、`\1` 來代表第一個擷取群組，`\2` 和以上的數字來代表後續的擷取群組。
* *文字*:字串。

**傳回**

以 rewrite** 的評估取代 regex** 的所有相符項目後的 text**。 相符項目不會重疊。

**範例**

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
 