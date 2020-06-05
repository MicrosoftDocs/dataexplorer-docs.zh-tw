---
title: mv-展開運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 mv-expand 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: de0b2696ba5974a7261b4f397214e3ade59fc9cb
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454094"
---
# <a name="mv-expand-operator"></a>mv-expand 運算子

展開多值陣列或屬性包。

`mv-expand`會套用在[動態](./scalar-data-types/dynamic.md)類型的資料行上，讓集合中的每個值都能取得個別的資料列。 所展開資料列中的其他所有資料行則會重複。 

**語法**

*T* `| mv-expand ` [ `bagexpansion=` （ `bag`  |  `array` ）] [ `with_itemindex=` *IndexColumnName*] *columnname* [ `,` *columnname* ...] [ `limit` *Rowlimit*]

*T* `| mv-expand ` [ `bagexpansion=` （ `bag`  |  `array` ）] [*Name* `=` ]*產生陣列*[ `to typeof(` *Typename* `)` ] [，[*Name* `=` ]*產生陣列*[ `to typeof(` *Typename* `)` ] ...] [ `limit` *Rowlimit*]

**引數**

* ** 在結果中，具名資料行中的陣列會展開為多個資料列。 
* ** 產生陣列的運算式。 如果使用這種形式，則會新增新資料行，並保留現有資料行。
* ** 新資料行的名稱。
* *Typename：* 指出陣列元素的基礎類型，這會成為運算子所產生之資料行的類型。 陣列中不符合的值將不會轉換。 相反地，這些值將會接受 `null` 值。
* ** 從每個原始資料列所產生的資料列數目上限。 預設值為2147483647。 
  > [!Note] 
  > 運算子的舊版和過時形式的 `mvexpand` 預設資料列限制為128。
* *IndexColumnName：* 如果 `with_itemindex` 指定了，輸出將會包含一個額外的資料行（名為*IndexColumnName*），其中包含原始展開集合中專案的索引（從0開始）。 

**傳回**

在命名或陣列運算式中，任何陣列中的每個值都有多個資料列。
如果指定了多個資料行或運算式，它們就會以平行方式展開。 針對每個輸入資料列，會有多個輸出資料列，因為最長的展開運算式中有元素（較短的清單會以 null 填補）。 如果資料列中的值是空的陣列，資料列就會展開為 [無] （不會顯示在結果集中）。 不過，如果資料列中的值不是陣列，資料列就會保留在結果集中。 

展開的資料行一律具有動態類型。 如果您想要計算或彙總多個值，請使用轉換函式，例如 `todatetime()` 或 `tolong()`。

有兩種屬性包展開模式可受到支援︰
* `bagexpansion=bag`︰屬性包會展開為單一項目屬性包。 此模式為預設展開。
* `bagexpansion=array`：屬性包會展開為兩個元素的索引 `[` *鍵值* `,` *value* `]` 陣列結構，允許對索引鍵和值的統一存取（也就是在屬性名稱上執行相異計數匯總）。 

**範例**

單一資料行的簡單擴充：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1"： "a"}|
|1|{"this.prop2"： "b"}|

將兩個數據行展開，會先「壓縮」適用的資料行，然後再加以展開：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b, c 
```

|a|b|c|
|---|---|---|
|1|{"prop1"： "a"}|5|
|1|{"this.prop2"： "b"}||

如果您想要取得可展開兩個數據行的笛卡兒乘積，請在另一個後面展開一個：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5])]
| mv-expand b 
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{"prop1"： "a"}|5|
|1|{"this.prop2"： "b"}|5|


陣列的擴充 `with_itemindex` ：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1 
| summarize x = make_list(x) 
| mv-expand with_itemindex=Index  x 
```

|x|索引|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|


**更多範例**

請參閱經過一段[時間的即時活動圖表計數](./samples.md#chart-concurrent-sessions-over-time)。

**另請參閱**

- [mv-apply](./mv-applyoperator.md)運算子。
- [摘要 make_list （）](makelist-aggfunction.md)，它會執行相反的函式。
- [bag_unpack （）](bag-unpackplugin.md)外掛程式，可使用屬性包索引鍵將動態 JSON 物件展開為數據行。
