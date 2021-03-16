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
ms.openlocfilehash: 956c24fa70df89f5bf99d4de12a8b07da6cb6912
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2021
ms.locfileid: "100359976"
---
# <a name="mv-expand-operator"></a>mv-expand 運算子

將多重值動態陣列或屬性包擴展為多筆記錄。

`mv-expand` 可以描述為將多個值封裝成單一 [動態](./scalar-data-types/dynamic.md)類型陣列或屬性包的匯總運算子，例如 `summarize` ... `make-list()` 和 `make-series` 。
 (純量) 陣列或屬性包中的每個元素都會在運算子的輸出中產生新的記錄。 未展開之輸入的所有資料行都會複製到輸出中的所有記錄。

## <a name="syntax"></a>Syntax

*T* `| mv-expand ` [ `bagexpansion=` (`bag`  |  `array`) ] [ `with_itemindex=` *IndexColumnName*] *ColumnName* [ `to typeof(` *Typename* `)` ] [ `,` *ColumnName* ...] [ `limit` *Rowlimit*]

*T* `| mv-expand ` [ `bagexpansion=` (`bag`  |  `array`) ] *Name* `=` *產生*[ `to typeof(` *typename* `)` ] [，[*Name* `=` ]*產生*[ `to typeof(` *typename* `)` ] ...] [ `limit` *Rowlimit*]

## <a name="arguments"></a>引數

* *ColumnName*、 *產生*：資料行參考，或具有類型值的純量運算式， `dynamic` 其中包含陣列或屬性包。 陣列或屬性包的個別最上層元素會展開為多筆記錄。<br>
  當使用 *產生* 且 *名稱* 不等於任何輸入資料行名稱時，展開的值會延伸至輸出中的新資料行。
  否則，就會取代現有的 *ColumnName* 。

*  新資料行的名稱。

* *Typename：* 指出陣列元素的基礎類型，這會成為 `mv-expand` 運算子所產生的資料行類型。 套用類型是只能轉換的作業，不會包含剖析或類型轉換。 不符合宣告型別的陣列元素將會變成 `null` 值。

*  從每個原始資料列所產生的資料列數目上限。 預設為 2147483647。 

  > [!NOTE]
  > `mvexpand` 是運算子 `mv-expand` 的舊版和過時形式。 舊版的預設資料列限制為 128 個。

* *IndexColumnName：* 如果 `with_itemindex` 指定了，則輸出會包含名為 *IndexColumnName* 的另一個資料行 () ，其中包含從原始展開集合中專案的 0) 開始的索引 (。 

## <a name="returns"></a>傳回

針對輸入中的每一筆記錄，運算子會在輸出中傳回零個、一個或多筆記錄，如下列方式所決定：

1. 未展開的輸入資料行會在輸出中顯示其原始值。
   如果將單一輸入記錄擴充為多個輸出記錄，此值會複製到所有記錄。

1. 針對每個擴充的 *ColumnName* 或 *產生*，輸出記錄的數目取決於每個值 [，如下所述。](#modes-of-expansion) 針對每個輸入記錄，會計算輸出記錄的最大數目。 所有陣列或屬性包都是以「平行」方式展開，所以如果有任何) 取代為 null 值，則會 (遺漏值。

1. 如果動態值為 null，則會產生該值的單一記錄 (null) 。
   如果動態值為空的陣列或屬性包，則不會針對該值產生任何記錄。
   否則，會隨著動態值中的元素而產生許多記錄。

展開的資料行是型別 `dynamic` ，除非使用子句明確地輸入資料行 `to typeof()` 。

### <a name="modes-of-expansion"></a>展開模式

支援兩種屬性包擴充模式：

* `bagexpansion=bag` 或 `kind=bag`：屬性包會展開為單一項目屬性包。 此模式為預設模式。
* `bagexpansion=array`或者 `kind=array` ：屬性包會展開為兩個專案的索引 `[` *鍵值* `,`  `]` 陣列結構，允許統一存取索引鍵和值。 例如，此模式也可讓您對屬性名稱執行相異計數匯總。 

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

若要強制將 mv 展開至特定類型 (預設為動態) ，請使用 `to typeof` ：

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

當傳回時，會傳回 [通知] 資料行 `b` `dynamic` `c` `int` 。

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
