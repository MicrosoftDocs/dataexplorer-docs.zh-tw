---
title: 降低操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的縮減運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d844f693b1509a823702b12bd28b85a9f19a07bd
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2020
ms.locfileid: "91102894"
---
# <a name="reduce-operator"></a>reduce 運算子

根據值相似性將一組字串群組在一起。

```kusto
T | reduce by LogMessage with threshold=0.1
```

針對每個這類群組，它會輸出一個最能描述該群組的**模式** (可能使用星號 (`*`) 字元來表示) **count**的萬用字元、群組中的值數目，以及群組的**代表** (群組) 中的其中一個原始值。

## <a name="syntax"></a>語法

*T* `|` `reduce` [ `kind` `=` *ReduceKind*] `by` *Expr* [ `with` [ `threshold` `=` *臨界值*] [ `,` `characters` `=` *字元*]]

## <a name="arguments"></a>引數

* *Expr*：評估為值的運算式 `string` 。
* *閾值*： `real` 範圍 (0 ..1) 的常值。 預設值為0.1。 若為大型輸入，臨界值應該小一點。 
* *字元*：常值， `string` 包含要加入不會中斷字詞之字元清單的字元清單。  (例如，如果您想要 `aaa=bbbb` 且 `aaa:bbb` 每個都是整個詞彙，而不是在 `=` 和上中斷 `:` ，請使用 `":="` 做為字串常值。 ) 
* *ReduceKind*：指定縮減類別。 目前唯一有效的值是 `source` 。

## <a name="returns"></a>傳回

這個運算子會傳回包含三個數據行的資料表 (`Pattern` 、 `Count` 和) ，以及與群組相同的資料列 `Representative` 數目。 `Pattern` 這是群組的模式值，用 `*` 來做為萬用字元 (代表任意的插入字串) 、 `Count` 計算運算子輸入中有多少資料列是由這個模式所表示，而這 `Representative` 是從屬於此群組的輸入中的一個值。

如果 `[kind=source]` 指定了，運算子就會將資料 `Pattern` 行附加至現有的資料表結構。
請注意，此類別的架構可能會受到未來變更的語法。

例如， `reduce by city` 的結果可能包括： 

|模式     |Count |Representative|
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

|模式         |Count|Representative   |
|----------------|-----|-----------------|
|MachineLearning|1000 |MachineLearningX4|

## <a name="examples"></a>範例

下列範例示範如何將運算子套用至「已清理」的 `reduce` 輸入，其中資料行中的 guid 會在減少之前被取代。

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

## <a name="see-also"></a>另請參閱

[autocluster](./autoclusterplugin.md)

**備註**

運算子的執行 `reduce` 主要是以「資料叢集演算法」一文為基礎，也就是 [從事件記錄](https://ristov.github.io/publications/slct-ipom03-web.pdf)檔中 Risto Vaarandi 的資料群集演算法。
