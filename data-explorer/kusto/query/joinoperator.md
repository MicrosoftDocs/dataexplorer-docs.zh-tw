---
title: join 運算子-Azure 資料總管 |Microsoft Docs
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
ms.openlocfilehash: 2961f27226175fe81b7d9c82a6366b36134d805f
ms.sourcegitcommit: 31af2dfa75b5a2f59113611cf6faba0b45d29eb5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454111"
---
# <a name="join-operator"></a>join 運算子

透過比對每個資料表中所指定資料行的值，來合併兩個資料表的資料列，以形成新的資料表。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**語法**

*LeftTable* `|``join`[*JoinParameters*] `(` *RightTable* `)` `on` *屬性*

**引數**

* *LeftTable*：要合併其資料列的**左側**資料表或表格式運算式（有時稱為**外部**資料表）。 表示為 `$left` 。

* *RightTable*：要合併其資料列的**右側**資料表或表格式運算式（有時稱為 **內部*資料表）。 表示為 `$right` 。

* *屬性*：一或多個（以逗號分隔）規則，描述*LeftTable*中的資料列如何與*RightTable*中的資料列比對。 使用邏輯運算子來評估多個規則 `and` 。
  規則可以是下列其中一個：

  |規則種類        |語法                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |依名稱相等 |*ColumnName*                                    |`where`*LeftTable*。*ColumnName* `==`*RightTable*。*ColumnName*|
  |依值相等|`$left.`*LeftColumn* `==``$right.` *RightColumn*|`where``$left.` *LeftColumn* `==` LeftColumn `$right.`*RightColumn*       |

> [!NOTE]
> 如果是「依值相等」，則*必須*以和標記法來限定資料行名稱，並以適用的擁有者資料表加以限定 `$left` `$right` 。

* *JoinParameters*：*名稱*值格式為零或多個（以空格分隔）的參數 `=` *Value* ，可控制資料列比對作業和執行計畫的行為。 支援下列參數： 

::: zone pivot="azuredataexplorer"

  |名稱           |值                                        |描述                                  |
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
> 如果未指定，預設的聯結類別 `kind` 為 `innerunique` 。 這不同于其他的分析產品，其具有做 `inner` 為預設的類別。 請[仔細閱讀，以](#join-flavors)瞭解不同類型之間的差異，並確保查詢會產生預期的結果。

**傳回**

輸出架構取決於聯結類別：

 * `kind=leftanti`, `kind=leftsemi`:

     結果資料表只包含左側的資料行。

     
 * `kind=rightanti`, `kind=rightsemi`:

     結果資料表只包含右側的資料行。

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。 如果名稱有衝突，右側的資料行會自動重新命名。

     
輸出記錄視聯結類別而定：

 * `kind=leftanti`, `kind=leftantisemi`

     傳回左邊沒有符合右邊的所有記錄。     
     
 * `kind=rightanti`, `kind=rightantisemi`

     傳回右邊沒有相符專案的所有記錄。  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    輸入資料表間的每個相符項目各自佔據一個資料列。 「比對」是從一個資料表中選取的資料列，其值 `on` 與具有下列條件約束之另一個資料表中的資料列相同：

   - `kind`識別`kind=innerunique`

    左側只會有一個資料列符合 `on` 索引鍵的每個值。 此資料列與右側資料列的每個相符項目都會在輸出中佔有一個資料列。
    
   - `kind=leftsemi`
   
    傳回左邊有符合右邊的所有記錄。
    
   - `kind=rightsemi`
   
       從右邊傳回具有相符專案的所有記錄。

   - `kind=inner`
 
    左側和右側相符資料列的每個組合都會在輸出中佔有一個資料列。

   - `kind=leftouter` (或 `kind=rightouter` 或 `kind=fullouter`)

    除了內部的相符項目，左側 (和/或右側) 的每個資料列即使不相符也會佔有一個資料列。 在此情況下，不相符的輸出資料格會包含 null。
    如果好幾個資料列在這些欄位具有相同的值，每一個組合都會佔有一個資料列。

 

**提示**

若要獲得最佳效能︰

* 如果某個資料表一定會比另一個資料表還小，請將它做為聯結的左側 (管線)。

**範例**

從記錄檔中取得擴充的活動，其中的某些項目會標示活動的開始和結束。 

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

[這個範例的詳細資訊](./samples.md#get-sessions-from-start-and-stop-events)。

## <a name="join-flavors"></a>聯結類別

聯結運算子的確切類別是以某種關鍵字來指定。 目前，Kusto 支援下列聯結運算子的種類： 

|聯結種類|描述|
|--|--|
|[`innerunique`](#default-join-flavor)（或做為預設值）|與左側重復資料刪除的內部聯結|
|[`inner`](#inner-join)|標準內部聯結|
|[`leftouter`](#left-outer-join)|左方外部聯結|
|[`rightouter`](#right-outer-join)|右方外部聯結|
|[`fullouter`](#full-outer-join)|完整外部聯結|
|[`leftanti`](#left-anti-join)、 [`anti`](#left-anti-join) 或[`leftantisemi`](#left-anti-join)|左方反聯結|
|[`rightanti`](#right-anti-join)或[`rightantisemi`](#right-anti-join)|右方反聯結|
|[`leftsemi`](#left-semi-join)|左方半聯結|
|[`rightsemi`](#right-semi-join)|右方半聯結|

### <a name="inner-and-innerunique-join-operator-flavors"></a>inner 和 innerunique join 運算子類別

-    使用**內部聯結**類別時，輸出中會有一個資料列，其中的每個相符資料列都是由左和右組成，而不會有 deduplications 的左索引鍵。 輸出將會是左和右鍵的笛卡兒乘積。
    **內部聯結**的範例：

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
| join kind = inner
    t2
on key
```

|索引鍵|value|key1|value1|
|---|---|---|---|
|1|val 1。2|1|val 1。3|
|1|val 1。1|1|val 1。3|
|1|val 1。2|1|val 1。4|
|1|val 1。1|1|val 1。4|

-    使用**innerunique 聯結**類別將會從左側刪除重複索引鍵，並在每次重復資料刪除的左邊鍵和右索引鍵組合的輸出中，會有一個資料列。
    針對上述使用的相同資料集進行**innerunique 聯結**的範例，請注意，此案例的**innerunique**類別可能會產生兩個可能的輸出，而且兩者都正確。
    在第一個輸出中，join 運算子會隨機挑選第一個出現在 t1 中且值為 "val 1.1" 的索引鍵，而在第二個索引鍵中，會以 t2 索引鍵來比對它，而第二個索引鍵則會出現在 t1 中，其值為 "val 1.2"，並與 t2 索引鍵相符：

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


-    Kusto 已優化，可在可能的情況下，將出現在 `join` 適當聯結側後方的篩選推播。
    有時候，當使用的類別是**innerunique** ，且篩選器可以傳播到聯結的左邊時，它會自動傳播，而套用至該篩選的索引鍵一律會出現在輸出中。
    例如，使用上述範例並加入篩選， ` where value == "val1.2" ` 一律會提供第二個結果，而且永遠不會提供所使用資料集的第一個結果：

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
 
### <a name="default-join-flavor"></a>預設聯結類別
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
讓我們使用兩個範例資料表來說明聯結作業： 
 
資料表 X 

|Key |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

資料表 Y 

|Key |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 
 
刪除聯結索引鍵左側的重複資料後，預設聯結會執行內部聯結 (重複資料刪除會保留第一筆記錄)。 假設陳述式： 

    X | join Y on Key 

則有效的聯結左側 (刪除重複資料後的資料表 X) 會是： 

|Key |Value1 
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

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|


（請注意，索引鍵 ' a ' 和 ' d ' 不會出現在輸出中，因為左邊和右邊沒有相符的索引鍵）。 
 
（在過去，這是初始版本 Kusto 所支援的第一次執行聯結; 在一般的記錄/追蹤分析案例中，我們想要將兩個事件（每個符合某些篩選準則）與相同的相互關聯識別碼相互關聯，並取回我們要尋找的所有現象外觀，忽略多個參與追蹤記錄的外觀）。
 
### <a name="inner-join"></a>內部聯結

這是 SQL 世界中眾所周知的標準內部聯結。 只要左側的記錄與右側的記錄有相同的聯結索引鍵，就會產生輸出記錄。 
 
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

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|

請注意，來自右側的 (b,10) 聯結了兩次：與左側的 (b,2) 和 (b,3) 兩者聯結；左側的 (c,4) 也聯結了兩次：與右側的 (c,20) 和 (c,30) 兩者聯結。 

### <a name="left-outer-join"></a>左方外部聯結 

資料表 X 和 Y 的左外部聯結結果包含左資料表 (X) 的所有記錄，即使聯結條件在右資料表 (Y) 中找不到任何符合的記錄。 
 
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

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

 
### <a name="right-outer-join"></a>右方外部聯結 

類似於左外部聯結，但資料表的處理方式相反。 
 
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

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

 
### <a name="full-outer-join"></a>完整外部聯結 

就概念而言，完整外部聯結結合了套用左外部聯結與右外部聯結的效果。 聯結資料表中的記錄不相符時，結果集對於資料表的每個資料行都不會有 Null 值。 針對相符的記錄，會在結果集中產生單一的資料列 (包含從兩個資料表填入的資料行)。 
 
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

|Key|Value1|Key1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

 
### <a name="left-anti-join"></a>左方反聯結

左方反聯結會傳回左側的所有記錄，而不符合右側的任何記錄。 
 
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

|Key|Value1|
|---|---|
|a|1|

反聯結建立「NOT IN」查詢模型。 

### <a name="right-anti-join"></a>右方反聯結

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

|Key|Value2|
|---|---|
|d|40|

反聯結建立「NOT IN」查詢模型。 

### <a name="left-semi-join"></a>左方半聯結

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

|Key|Value1|
|---|---|
|b|3|
|b|2|
|c|4|

### <a name="right-semi-join"></a>右方半聯結

右方半聯結會從左側傳回符合一筆記錄的所有記錄。 只會傳回右側的資料行。 

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

|Key|Value2|
|---|---|
|b|10|
|c|20|
|c|30|


### <a name="cross-join"></a>交叉聯結

Kusto 不會以原生方式提供交叉聯結類別（亦即，您無法將運算子標示為 `kind=cross` ）。
不過，我們不太容易模擬這種情況，而是透過虛擬機器碼：

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>聯結提示

`join`運算子支援一些可控制查詢執行方式的提示。
這些不會變更的語義 `join` ，但可能會影響其效能。

下列文章中說明的聯結提示： 
* `hint.shufflekey=<key>`和 `hint.strategy=shuffle`  -  [隨機查詢](shufflequery.md)
* `hint.strategy=broadcast` - [廣播聯結](broadcastjoin.md)
* `hint.remote=<strategy>` - [跨叢集聯結](joincrosscluster.md)
