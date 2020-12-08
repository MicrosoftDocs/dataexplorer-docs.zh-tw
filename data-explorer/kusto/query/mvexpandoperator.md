---
title: mv-expand 運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 mv-expand 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2019
ms.localizationpriority: high
ms.openlocfilehash: f14ec4fa24765053711d60f7d2365755b45adbab
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774634"
---
# <a name="mv-expand-operator"></a>mv-expand 運算子

擴充多值陣列或屬性包。

`mv-expand` 會套用在[動態](./scalar-data-types/dynamic.md)類型的陣列或屬性包資料行上，讓集合中的每個值都能取得個別的資料列。 所展開資料列中的其他所有資料行則會重複。 

## <a name="syntax"></a>語法

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [`with_itemindex=`*IndexColumnName*] *ColumnName* [`,` *ColumnName* ...] [`limit` *Rowlimit*]

*T* `| mv-expand ` [`bagexpansion=`(`bag` | `array`)] [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] [, [*Name* `=`] *ArrayExpression* [`to typeof(`*Typename*`)`] ...] [`limit` *Rowlimit*]

## <a name="arguments"></a>引數

*  在結果中，具名資料行中的陣列會展開為多個資料列。 
*  產生陣列的運算式。 如果使用這種形式，則會新增新資料行，並保留現有資料行。
*  新資料行的名稱。
* *Typename：* 指出陣列元素的基礎類型，這會成為 `mv-apply` 運算子所產生的資料行類型。 套用類型是只能轉換的作業，不會包含剖析或類型轉換。 不符合宣告類型的陣列元素會變成 `null` 值。
*  從每個原始資料列所產生的資料列數目上限。 預設為 2147483647。 

  > [!NOTE]
  > `mvexpand` 是運算子 `mv-expand` 的舊版和過時形式。 舊版的預設資料列限制為 128 個。

* *IndexColumnName：* 如果指定了 `with_itemindex`，則輸出會包含額外的資料行 (名為 *IndexColumnName*)，其中包含原始已擴充集合中項目的索引 (從 0 開始)。 

## <a name="returns"></a>傳回

具名資料行的任何陣列中或是陣列運算式中的每個值會佔據多個資料列。
如果指定了多個資料行或運算式，則會以平行方式擴充。 針對每個輸入資料列，會有與擴充時最長的運算式中所含有的元素數目一樣多的輸出資料列 (較短的清單會以 Null 填補)。 如果資料列中的值是空陣列，資料列則不會擴充 (不會顯示在結果集內)。 不過，如果資料列中的值不是陣列，則資料列就會保持在和結果集一樣的狀態。 

展開的資料行一律具有動態類型。 如果您想要計算或彙總多個值，請使用轉換函式，例如 `todatetime()` 或 `tolong()`。

有兩種屬性包展開模式可受到支援︰
* `bagexpansion=bag` 或 `kind=bag`：屬性包會展開為單一項目屬性包。 此模式為預設擴充。
* `bagexpansion=array` 或 `kind=array`：屬性包會展開為有兩個項目 `[`key`,`value`]` 的陣列結構，以便能夠統一存取金鑰和值 (以及舉例來說，對屬性名稱執行相異計數彙總)。 

## <a name="examples"></a>範例

### <a name="single-column"></a>單一資料行

單一資料行的簡單擴充：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
 ```kusto
datatable (a:int, b:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"})]
| mv-expand b 
```

|a|b|
|---|---|
|1|{"prop1":"a"}|
|1|{"prop2":"b"}|

### <a name="zipped-two-columns"></a>已壓縮的兩個資料行

擴充兩個資料行會先「壓縮」適用的資料行，然後再加以擴充：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)[1,dynamic({"prop1":"a", "prop2":"b"}), dynamic([5, 4, 3])]
| mv-expand b, c
```

|a|b|c|
|---|---|---|
|1|{"prop1":"a"}|5|
|1|{"prop2":"b"}|4|
|1||3|

### <a name="cartesian-product-of-two-columns"></a>兩個資料行的笛卡兒乘積

如果您想要取得擴充兩個資料行的笛卡兒乘積，請一個接著一個地擴充資料行：

<!-- csl: https://kuskusdfv3.kusto.windows.net/Kuskus -->
```kusto
datatable (a:int, b:dynamic, c:dynamic)
  [
  1,
  dynamic({"prop1":"a", "prop2":"b"}),
  dynamic([5, 6])
  ]
| mv-expand b
| mv-expand c
```

|a|b|c|
|---|---|---|
|1|{  "prop1": "a"}|5|
|1|{  "prop1": "a"}|6|
|1|{  "prop2": "b"}|5|
|1|{  "prop2": "b"}|6|

### <a name="convert-output"></a>轉換輸出

如果您想要強制將 mv-expand 的輸出設為特定類型 (預設為動態)，請使用 `to typeof`：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
datatable (a:string, b:dynamic, c:dynamic)["Constant", dynamic([1,2,3,4]), dynamic([6,7,8,9])]
| mv-expand b, c to typeof(int)
| getschema 
```

ColumnName|ColumnOrdinal|DateType|ColumnType
-|-|-|-
a|0|System.String|字串
b|1|System.Object|動態
c|2|System.Int32|int

請注意，資料行 `b` 會以 `dynamic` 的形式出現，而 `c` 則會以 `int` 的形式出現。

### <a name="using-with_itemindex"></a>使用 with_itemindex

使用 `with_itemindex` 擴充陣列：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 4 step 1
| summarize x = make_list(x)
| mv-expand with_itemindex=Index x
```

|x|索引|
|---|---|
|1|0|
|2|1|
|3|2|
|4|3|
 
## <a name="see-also"></a>另請參閱

* 如需更多範例，請參閱[一段時間的即時活動圖表計數](./samples.md#chart-concurrent-sessions-over-time)。
* [mv-apply](./mv-applyoperator.md) 運算子。
* [summarize make_list()](makelist-aggfunction.md)，這是 mv-expand 的反函式。
* [bag_unpack()](bag-unpackplugin.md) 外掛程式，可使用屬性包索引鍵將動態 JSON 物件擴充至資料行。
