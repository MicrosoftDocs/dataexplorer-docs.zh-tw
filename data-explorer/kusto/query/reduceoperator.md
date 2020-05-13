---
title: 減少操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的縮減運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d157244a167e1264b454cd8cd3c103e297c3263
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373059"
---
# <a name="reduce-operator"></a>reduce 運算子

根據值的相似性，將一組字串分組在一起。

```kusto
T | reduce by LogMessage with threshold=0.1
```

針對每個這類群組，它會輸出最能描述該群組的**模式**（可能使用星號（ `*` ）字元來代表萬用字元） **count** 、群組中的值數目，以及群組的**代表**（群組中的其中一個原始值）。

**語法**

*T* `|` `reduce` [ `kind` `=` *ReduceKind*] `by` *Expr* [ `with` [ `threshold` `=` *閾值*] [ `,` `characters` `=` *字元*]]

**引數**

* *Expr*：評估為值的運算式 `string` 。
* *閾值*： `real` 範圍（0 ..1）中的常值。 預設值為0.1。 若為大型輸入，臨界值應該小一點。 
* *字元*：常值， `string` 其中包含要加入不會中斷字詞之字元清單中的字元清單。 （例如，如果您希望 `aaa=bbbb` 和 `aaa:bbb` 都是整個詞彙，而不是在和上中斷 `=` ， `:` 請使用 `":="` 做為字串常值）。
* *ReduceKind*：指定 [縮減] 類別。 唯一有效的時間值為 `source` 。

**傳回**

這個運算子會傳回具有三個數據行（、和）的資料表，以及與群組相同的資料列 `Pattern` `Count` `Representative` 數目。 `Pattern`這是群組的模式值，用 `*` 來做為萬用字元（代表任意插入字串）、 `Count` 計算運算子的輸入中有多少資料列是由這個模式表示，而且 `Representative` 是來自屬於此群組之輸入的一個值。

如果 `[kind=source]` 指定了，運算子就會將資料 `Pattern` 行附加至現有的資料表結構。
請注意，此類別架構的語法可能會受到未來變更的攻擊。

例如， `reduce by city` 的結果可能包括： 

|模式     |計數 |Representative|
|------------|------|--------------|
| San *      | 5182 |San Bernard   |
| Saint *    | 2846 |聖 Lucy    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |一對一  |
| Paris      | 2716 |Paris         |

另一個具有自訂 token 化的範例：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|模式         |計數|Representative   |
|----------------|-----|-----------------|
|MachineLearning|1000 |MachineLearningX4|

**範例**

下列範例示範如何將運算子套用至「已清理」的 `reduce` 輸入，其中要減少的資料行中的 guid 會在減少之前被取代

```kusto
// Start with a few records from the Trace table.
Trace | take 10000
// We will reduce the Text column which includes random GUIDs.
// As random GUIDs interfere with the reduce operation, replace them all
// by the string "GUID".
| extend Text=replace(@"[[:xdigit:]]{8}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{4}-[[:xdigit:]]{12}", @"GUID", Text)
// Now perform the reduce. In case there are other "quasi-random" identifiers with embedded '-'
// or '_' characters in them, treat these as non-term-breakers.
| reduce by Text with characters="-_"
```

**另請參閱**

[autocluster](./autoclusterplugin.md)

**注意事項**

運算子的執行 `reduce` 主要是根據[來自事件記錄檔的資料群集演算法](https://ristov.github.io/publications/slct-ipom03-web.pdf)（Risto Vaarandi）。
