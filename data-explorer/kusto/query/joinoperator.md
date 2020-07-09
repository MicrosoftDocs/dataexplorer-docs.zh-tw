---
title: join 運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的 join 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4952e315a974e72135c722b255a96f57bf89cc12
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058774"
---
# <a name="join-operator"></a>join 運算子

藉由比對每個資料表中指定之資料行的值，來合併兩個數據表的資料列，以形成新的資料表。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>語法

*LeftTable* `|``join`[*JoinParameters*] `(` *RightTable* `)` `on` *屬性*

## <a name="arguments"></a>引數

* *LeftTable*：**左側**資料表或表格式運算式，有時稱為**外部**資料表，其資料列將會合並。 表示為 `$left` 。

* *RightTable*：**右側**資料表或表格式運算式，有時稱為**內部**資料表，其資料列將會合並。 表示為 `$right` 。

* *屬性*：一或多個以逗號分隔的**規則**，描述*LeftTable*中的資料列如何與*RightTable*中的資料列比對。 使用邏輯運算子來評估多個規則 `and` 。

  **規則**可以是下列其中一個：

  |規則種類        |語法          |Predicate    |
  |-----------------|--------------|-------------------------|
  |依名稱相等 |*ColumnName*    |`where`*LeftTable*。*ColumnName* `==`*RightTable*。*ColumnName*|
  |依值相等|`$left.`*LeftColumn* `==``$right.` *RightColumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.`*RightColumn*       |

    > [!NOTE]
    > 對於「依值相等」，資料行名稱*必須*以和標記法所代表的適用擁有者資料表加以限定 `$left` `$right` 。

* *JoinParameters*：*名稱*值格式的零或多個以空格分隔的參數 `=` *Value* ，可控制資料列比對作業和執行計畫的行為。 支援下列參數：

    ::: zone pivot="azuredataexplorer"

    |參數名稱           |值                                        |描述                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |聯結類別|查看[聯結](#join-flavors)類別|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |請參閱[跨叢集聯結](joincrosscluster.md)|
    |`hint.strategy`|執行提示                               |請參閱[聯結提示](#join-hints)                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |名稱           |值                                        |描述                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |聯結類別|查看[聯結](#join-flavors)類別|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|執行提示                               |請參閱[聯結提示](#join-hints)                |

    ::: zone-end

> [!WARNING]
> 如果 `kind` 未指定，則預設的聯結風格為 `innerunique` 。 這與其他具有做為預設類別的其他分析產品不同 `inner` 。  請參閱[聯結](#join-flavors)種類以瞭解差異，並確保查詢會產生預期的結果。

## <a name="returns"></a>傳回

**輸出架構取決於聯結類別：**

| 聯結類別 | 輸出結構描述 |
|---|---|
|`kind=leftanti`, `kind=leftsemi`| 結果資料表只包含左側的資料行。|
| `kind=rightanti`, `kind=rightsemi` | 結果資料表只包含右側的資料行。|
|  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter` |  兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。 如果名稱有衝突，右側的資料行會自動重新命名。 |
   
**輸出記錄取決於聯結類別：**

   > [!NOTE]
   > 如果好幾個資料列在這些欄位具有相同的值，每一個組合都會佔有一個資料列。
   > 其中一個資料表中選取的資料列如果和另一個資料表中的資料列在所有 `on` 欄位的值皆相同，就代表是相符項目。

| 聯結類別 | 輸出記錄 |
|---|---|
|`kind=leftanti`, `kind=leftantisemi`| 傳回左邊沒有符合右邊的所有記錄|
| `kind=rightanti`, `kind=rightantisemi`| 傳回右邊沒有相符專案的所有記錄。|
| `kind`識別`kind=innerunique`| 左側只會有一個資料列符合 `on` 索引鍵的每個值。 此資料列與右側資料列的每個相符項目都會在輸出中佔有一個資料列。|
| `kind=leftsemi`| 傳回左邊有符合右邊的所有記錄。 |
| `kind=rightsemi`| 從右邊傳回具有相符專案的所有記錄。 |
|`kind=inner`| 在輸出中，針對每個相符資料列的左邊和右邊，各包含一個資料列。 |
| `kind=leftouter` (或 `kind=rightouter` 或 `kind=fullouter`)| 針對左邊和右邊的每個資料列都包含一個資料列，即使它沒有相符項。 不相符的輸出資料格包含 null。 |

> [!TIP]
> 為了達到最佳效能，如果其中一個資料表永遠小於另一個，請使用它做為聯結的左側（輸送）端。

## <a name="example"></a>範例

從取得擴充的活動 `login` ，其中某些專案會標示為活動的開始和結束。

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityId, StartTime=timestamp
| join (Events
    | where Name == "Stop"
        | project StopTime=timestamp, ActivityId)
    on ActivityId
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

```kusto
let Events = MyLogTable | where type=="Event" ;
Events
| where Name == "Start"
| project Name, City, ActivityIdLeft = ActivityId, StartTime=timestamp
| join (Events
        | where Name == "Stop"
        | project StopTime=timestamp, ActivityIdRight = ActivityId)
    on $left.ActivityIdLeft == $right.ActivityIdRight
| project City, ActivityId, StartTime, StopTime, Duration = StopTime - StartTime
```

## <a name="join-flavors"></a>聯結類別

聯結運算子的確切類別是以*kind*關鍵字指定。 支援下列聯結運算子的種類：

|聯結種類/類別|描述|
|--|--|
|[`innerunique`](#default-join-flavor)（或做為預設值）|與左側重復資料刪除的內部聯結|
|[`inner`](#inner-join-flavor)|標準內部聯結|
|[`leftouter`](#left-outer-join-flavor)|左方外部聯結|
|[`rightouter`](#right-outer-join-flavor)|右方外部聯結|
|[`fullouter`](#full-outer-join-flavor)|完整外部聯結|
|[`leftanti`](#left-anti-join-flavor)、 [`anti`](#left-anti-join-flavor) 或[`leftantisemi`](#left-anti-join-flavor)|左方反聯結|
|[`rightanti`](#right-anti-join-flavor)或[`rightantisemi`](#right-anti-join-flavor)|右方反聯結|
|[`leftsemi`](#left-semi-join-flavor)|左方半聯結|
|[`rightsemi`](#right-semi-join-flavor)|右方半聯結|

### <a name="default-join-flavor"></a>預設聯結類別

預設的聯結類別是具有左側重復資料刪除的內部聯結。 在一般的記錄/追蹤分析案例中，如果您想要將兩個事件相互關聯，而且每個事件都符合相同的相互關聯識別碼，則預設聯結執行會非常有用。 您想要取回所有的現象外觀，並忽略參與追蹤記錄的多種外觀。

``` 
X | join Y on Key
 
X | join kind=innerunique Y on Key
```

下列兩個範例資料表可用來說明聯結的作業。

**資料表 X**

|答案 |Value1
|---|---
|a |1
|b |2
|b |3
|c |4

**資料表 Y**

|答案 |Value2
|---|---
|b |10
|c |20
|c |30
|d |40

預設聯結會在刪除重復資料聯結索引鍵的左側之後，進行內部聯結（重復資料刪除會保留第一筆記錄）。

假設有這個語句：`X | join Y on Key`

聯結的有效左邊，在重復資料刪除之後的資料表 X 會是：

|答案 |Value1
|---|---
|a |1
|b |2
|c |4

且聯結的結果會是：

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join Y on Key
```

|答案|Value1|Key1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> 索引鍵 ' a ' 和 ' d ' 不會出現在輸出中，因為左右兩側沒有相符的索引鍵。

### <a name="inner-join-flavor"></a>內部聯結類別

內部聯結函式就像是來自 SQL 世界的標準內部聯結。 每當左側的記錄與右側的記錄具有相同的聯結索引鍵時，就會產生輸出記錄。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=inner Y on Key
```

|答案|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

> [!NOTE]
> * （b，10）從右邊聯結了兩次：使用左側的（b，2）和（b，3）。
> * （c，4）在左邊，聯結了兩次：（c，20）和右邊的（c，30）。

### <a name="innerunique-join-flavor"></a>Innerunique-聯結類別
 
使用 [ **innerunique]-[聯結**] 類別，從左側刪除重複索引鍵。 結果會是每個重復資料刪除之左側索引鍵和右邊鍵組合的輸出中的一個資料列。

> [!NOTE]
> **innerunique**類別可能會產生兩個可能的輸出，而且兩者都正確。
在第一個輸出中，join 運算子會隨機選取出現在 t1 中的第一個索引鍵，其值為 "val 1.1"，並與 t2 索引鍵相符。
在第二個輸出中，join 運算子會隨機選取出現在 t1 中的第二個索引鍵，其值為 "val 1.2"，並與 t2 索引鍵相符。

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3",
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|索引鍵|value|key1|value1|
|---|---|---|---|
|1|val 1。1|1|val 1。3|
|1|val 1。1|1|val 1。4|

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
```

|索引鍵|value|key1|value1|
|---|---|---|---|
|1|val 1。2|1|val 1。3|
|1|val 1。2|1|val 1。4|

* Kusto 已優化，可將後面的篩選準則推送至 `join` 適當的聯結側（如果可能的話）。

* 有時候，使用的類別是**innerunique** ，而篩選會傳播到聯結的左邊。 類別會自動傳播，而套用至該篩選準則的金鑰一律會出現在輸出中。
    
* 使用上述範例，並新增篩選準則 `where value == "val1.2" ` 。 它一律會提供第二個結果，而且永遠不會提供資料集的第一個結果：

```kusto
let t1 = datatable(key:long, value:string)  
[
1, "val1.1",  
1, "val1.2"  
];
let t2 = datatable(key:long, value:string)  
[  
1, "val1.3", 
1, "val1.4"  
];
t1
| join kind = innerunique
    t2
on key
| where value == "val1.2"
```

|索引鍵|value|key1|value1|
|---|---|---|---|
|1|val 1。2|1|val 1。3|
|1|val 1。2|1|val 1。4|

### <a name="left-outer-join-flavor"></a>左方外部-聯結類別

資料表 X 和 Y 的左方外部聯結的結果一律包含左資料表（X）的所有記錄，即使聯結條件在右資料表（Y）中找不到任何相符的記錄。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftouter Y on Key
```

|答案|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

### <a name="right-outer-join-flavor"></a>右方外部-聯結類別

右外部聯結類別類似于左方外部聯結，但資料表的處理方式是相反的。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightouter Y on Key
```

|答案|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

### <a name="full-outer-join-flavor"></a>完整外部聯結類別

完整外部聯結結合了套用 left 和 right 外部聯結的效果。 每當聯結資料表中的記錄不相符時，結果集就會有資料表中每個資料行的值，而不會有 `null` 相符的資料列。 對於符合的記錄，會在結果集中產生單一資料列，其中包含從兩個數據表填入的欄位。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=fullouter Y on Key
```

|答案|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

### <a name="left-anti-join-flavor"></a>左方反聯結類別

左方反聯結會傳回左邊的所有記錄，而不符合右側的任何記錄。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftanti Y on Key
```

|答案|Value1|
|---|---|
|a|1|

> [!NOTE]
> 反聯結建立「NOT IN」查詢模型。

### <a name="right-anti-join-flavor"></a>右方反聯結類別

右方反聯結會從右側傳回不符合左側任何記錄的所有記錄。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightanti Y on Key
```

|答案|Value2|
|---|---|
|d|40|

> [!NOTE]
> 反聯結建立「NOT IN」查詢模型。

### <a name="left-semi-join-flavor"></a>左方半聯結類別

左方半聯結會從右邊傳回符合記錄的所有記錄。 只會傳回左邊的資料行。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=leftsemi Y on Key
```

|答案|Value1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join-flavor"></a>右方半聯結類別

右方半聯結會從左側傳回符合記錄的所有記錄。 只會傳回右側的資料行。

```kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X | join kind=rightsemi Y on Key
```

|答案|Value2|
|---|---|
|b|10|
|c|20|
|c|30|

### <a name="cross-join"></a>交叉聯結

Kusto 原本不提供交叉聯結的類別。 您無法使用來標記運算子 `kind=cross` 。
若要模擬，請使用虛擬金鑰。

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>聯結提示

`join`運算子支援一些可控制查詢執行方式的提示。
這些提示不會變更的語義 `join` ，但可能會影響其效能。

下列文章將說明聯結提示：

* `hint.shufflekey=<key>`和 `hint.strategy=shuffle`  -  [隨機查詢](shufflequery.md)
* `hint.strategy=broadcast` - [廣播聯結](broadcastjoin.md)
* `hint.remote=<strategy>` - [跨叢集聯結](joincrosscluster.md)
