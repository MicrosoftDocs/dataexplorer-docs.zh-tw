---
title: 減少運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的縮減運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33d4ac202b61fdaa1b92291407cdd2783d947c6e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510501"
---
# <a name="reduce-operator"></a>reduce 運算子

根據值相似性將一組字串組合在一起。

```kusto
T | reduce by LogMessage with threshold=0.1
```

此一個所有此類群組,輸出一個**模式**,以最好地描述群組(可能使用 asterix`*`( ) 字元來表示通配符、群組中的值數**計數**以及群組(群組中的原始值之一)**的表示形式**。

**語法**

*T* `|` T `threshold` `kind`=`=`減少金德 • Expr [ 閾值 ] = 字元 * `reduce` `characters` `=` `,` `=` *Characters* *ReduceKind* `by` *Expr* `with` * *

**引數**

* *Expr*:一個計算到值`string`的 運算式。
* *閾值*`real`:範圍中的文本 (0..1)。 默認值為 0.1。 若為大型輸入，臨界值應該小一點。 
* *字元*:`string`包含 要加入不中斷術語的字元清單中的字元列表的文字。 (`aaa=bbbb`例如,如果希望`aaa:bbb`並且 每個術語都是整個術語,而不是`=`中`:`斷 和`":="`,用作 字串文本。
* *減少金德*: 指定減少的味道。 目前唯一有效的值是`source`。

**傳回**

此運算符返回具有三列 (、`Pattern``Count``Representative`和 ) 的表,以及與組一樣多的行。 `Pattern`是群組的模式值,`*`用作通配符(表示任意插入字串),`Count`計算該模式表示運算符號輸入的行數,`Representative`並且是屬於此組的輸入中的一個值。

如果`[kind=source]`指定,運算子將該`Pattern`列追加到現有表結構。
請注意,此風格的架構的語法可能會受到將來的更改。

例如， `reduce by city` 的結果可能包括： 

|模式     |Count |Representative|
|------------|------|--------------|
| San *      | 5182 |聖伯納德   |
| Saint *    | 2846 |聖露西    |
| Moscow     | 3726 |Moscow        |
| \* -on- \* | 2730 |一對一  |
| Paris      | 2716 |Paris         |

具有自訂權杖化的另一個範例:

```kusto
range x from 1 to 1000 step 1
| project MyText = strcat("MachineLearningX", tostring(toint(rand(10))))
| reduce by MyText  with threshold=0.001 , characters = "X" 
```

|模式         |Count|Representative   |
|----------------|-----|-----------------|
|機器學習*|1000 |機器學習X4|

**範例**

下面的範例展示如何將`reduce`運算子應用於「已消毒」輸入,在減少列的 GUID 之前取代

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

運算元的`reduce`實現主要基於 Risto Vaarandi 的論文《[事件日誌中挖掘模式的數據聚類演演演算法](https://ristov.github.io/publications/slct-ipom03-web.pdf)》。