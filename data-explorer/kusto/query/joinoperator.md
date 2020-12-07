---
title: join 運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的聯結運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b90e5f1c95ec75a946490cd75b5dd89ad2cb1aba
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513330"
---
# <a name="join-operator"></a>join 運算子

透過比對每個資料表中所指定資料行的值，來合併兩個資料表的資料列，以形成新的資料表。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

## <a name="syntax"></a>語法

*LeftTable* `|` `join` [*JoinParameters*] `(` *RightTable* `)` `on` *Attributes*

## <a name="arguments"></a>引數

* *LeftTable*：**左側** 資料表或表格式運算式，有時稱為 **外部** 資料表，其資料列將遭到合併。 表示方法為 `$left`。

* *RightTable*：**右側** 資料表或表格式運算式，有時稱為 **內部** 資料表，其資料列將遭到合併。 表示方法為 `$right`。

* *Attributes*：一或多個以逗號分隔的 **規則**，這些規則會描述 *LeftTable* 的資料列如何與 *RightTable* 的資料列進行比對。 使用 `and` 邏輯運算子即可評估多個規則。

  **規則** 可以是下列其中一種：

  |規則種類        |語法          |Predicate    |
  |-----------------|--------------|-------------------------|
  |依名稱相等 |*ColumnName*    |`where` *LeftTable*.*ColumnName* `==` *RightTable*.*ColumnName*|
  |依值相等|`$left.`*LeftColumn* `==` `$right.`*RightColumn*|`where` `$left.`*LeftColumn* `==` `$right.`*RightColumn*       |

    > [!NOTE]
    > 若為「依值相等」，則資料行名稱 *必須* 以 `$left` 和 `$right` 標記法所代表的適用擁有者資料表來加以限定。

* *JoinParameters*：格式為 *Name* `=` *Value* 的零個或多個 (以空格分隔) 參數，此種參數可控制資料列比對作業和執行計畫的行為。 支援下列參數：

    ::: zone pivot="azuredataexplorer"

    |參數名稱           |值                                        |描述                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |聯結類別|請參閱[聯結類別](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |請參閱[跨叢集聯結](joincrosscluster.md)|
    |`hint.strategy`|執行提示                               |請參閱[聯結提示](#join-hints)                |

    ::: zone-end

    ::: zone pivot="azuremonitor"

    |名稱           |值                                        |描述                                  |
    |---------------|----------------------------------------------|---------------------------------------------|
    |`kind`         |聯結類別|請參閱[聯結類別](#join-flavors)|                                             |
    |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
    |`hint.strategy`|執行提示                               |請參閱[聯結提示](#join-hints)                |

    ::: zone-end

> [!WARNING]
> 如果未指定 `kind`，則預設的聯結類別是 `innerunique`。 這與其他某些分析產品會以 `inner` 作為預設類別的行為不同。  請參閱[聯結類別](#join-flavors)以了解差異，並確保查詢會產生預期的結果。

## <a name="returns"></a>傳回

**輸出結構描述取決於聯結類別：**

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
|`kind=leftanti`, `kind=leftantisemi`| 傳回左側中與右側不相符的所有記錄|
| `kind=rightanti`, `kind=rightantisemi`| 傳回右側中與左側不相符的所有記錄。|
| `kind` 未指定，`kind=innerunique`| 左側只會有一個資料列符合 `on` 索引鍵的每個值。 此資料列與右側資料列的每個相符項目都會在輸出中佔有一個資料列。|
| `kind=leftsemi`| 傳回左側中與右側相符的所有記錄。 |
| `kind=rightsemi`| 傳回右側中與左側相符的所有記錄。 |
|`kind=inner`| 左側和右側相符資料列的每個組合都會在輸出中含有一個資料列。 |
| `kind=leftouter` (或 `kind=rightouter` 或 `kind=fullouter`)| 左側和右側的每個資料列都含有一個資料列，即使沒有相符項目也一樣。 不相符的輸出資料格會包含 null。 |

> [!TIP]
> 為獲得最佳效能，如果某個資料表一定會比另一個資料表還小，請將其作為聯結的左側 (管線)。

## <a name="example"></a>範例

從 `login` 中取得擴充的活動，其中的某些項目會標示為活動的開始和結束。

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

聯結運算子的確切類別會以 *kind* 關鍵字來加以指定。 支援下列聯結運算子類別：

|聯結種類/類別|描述|
|--|--|
|[`innerunique`](#default-join-flavor) (或以空白作為預設值)|會將左側重復資料刪除的內部聯結|
|[`inner`](#inner-join-flavor)|標準內部聯結|
|[`leftouter`](#left-outer-join-flavor)|左方外部聯結|
|[`rightouter`](#right-outer-join-flavor)|右方外部聯結|
|[`fullouter`](#full-outer-join-flavor)|完整外部聯結|
|[`leftanti`](#left-anti-join-flavor)、[`anti`](#left-anti-join-flavor) 或 [`leftantisemi`](#left-anti-join-flavor)|左方反聯結|
|[`rightanti`](#right-anti-join-flavor) 或 [`rightantisemi`](#right-anti-join-flavor)|右方反聯結|
|[`leftsemi`](#left-semi-join-flavor)|左方半聯結|
|[`rightsemi`](#right-semi-join-flavor)|右方半聯結|

### <a name="default-join-flavor"></a>預設聯結類別

預設的聯結類別是會將左側重復資料刪除的內部聯結。 在典型的記錄/追蹤分析案例中，如果您想要將兩個事件相互關聯，而且每個事件都符合相同的相互關聯識別碼底下的某些篩選準則，則實作預設聯結會非常有用。 您可以取回某個現象的所有出現次數，並忽略造成追蹤記錄的多重出現次數。

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

刪除聯結索引鍵左側的重複資料後，預設聯結會執行內部聯結 (重複資料刪除會保留第一筆記錄)。

假設陳述式如下：`X | join Y on Key`

則有效的聯結左側 (刪除重複資料後的資料表 X) 會是：

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
> 索引鍵 'a' 和 'd' 不會出現在輸出中，因為在左側和右側沒有相符的索引鍵。

### <a name="inner-join-flavor"></a>內部聯結類別

內部聯結函式就像是 SQL 世界中的標準內部聯結。 只要左側的記錄與右側的記錄有相同的聯結索引鍵，就會產生輸出記錄。

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
> * 右側的 (b,10) 聯結了兩次：聯結對象為左側的 (b,2) 和 (b,3)。
> * 左側的 (c,4) 聯結了兩次：聯結對象為右側的 (c,20) 和 (c,30)。

### <a name="innerunique-join-flavor"></a>Innerunique 聯結類別
 
使用 **Innerunique 聯結類別** 可將左側的重複索引鍵刪除。 結果會是每個已刪除重復資料的左側索引鍵和右側索引鍵組合所產生輸出中的一個資料列。

> [!NOTE]
> **innerunique 類別** 可能會產生兩種可能的輸出，而且兩者都正確。
在第一個輸出中，join 運算子會隨機選取出現在 t1 中的第一個索引鍵，其值為 "val1.1"，並將其與 t2 索引鍵進行比對。
在第二個輸出中，join 運算子會隨機選取出現在 t1 中的第二個索引鍵，其值為 "val1.2"，並將其與 t2 索引鍵進行比對。

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
|1|val1.1|1|val1.3|
|1|val1.1|1|val1.4|

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
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

* Kusto 會進行最佳化，如果可以的話，就會將 `join` 之後才來的篩選推送至適當的聯結側 (左側或右側)。

* 有時候，所使用的類別是 **innerunique**，因此篩選會傳播至聯結的左邊。 類別會自動傳播，而套用至該篩選的索引鍵一律會出現在輸出中。
    
* 使用上述範例並新增篩選 `where value == "val1.2" `。 針對資料集，其一律會提供第二個結果，永遠不會提供第一個結果：

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
|1|val1.2|1|val1.3|
|1|val1.2|1|val1.4|

### <a name="left-outer-join-flavor"></a>左側外部聯結類別

資料表 X 和 Y 的左側外部聯結結果包含左資料表 (X) 的所有記錄，即使聯結條件在右側資料表 (Y) 中找不到任何符合的記錄。

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

### <a name="right-outer-join-flavor"></a>右側外部聯結類別

右側外部聯結類別類似於左側外部聯結，但資料表的處理方式相反。

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

完整外部聯結結合了，套用左側外部聯結與右側外部聯結的效果。 每當所聯結資料表的記錄不相符，對於每個缺少相符資料列的資料表所含的每個資料行，結果集將會有 `null` 值。 針對相符的記錄，會在結果集中產生單一的資料列 (包含從兩個資料表填入的資料行)。

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

### <a name="left-anti-join-flavor"></a>左側反聯結類別

左側反聯結會傳回左側中與右側任何記錄都不相符的所有記錄。

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

### <a name="right-anti-join-flavor"></a>右側反聯結類別

右側反聯結會傳回右側中與左側任何記錄都不相符的所有記錄。

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

### <a name="left-semi-join-flavor"></a>左側半聯結類別

左側半聯結會傳回左側中與右側記錄相符的所有記錄。 只會傳回左側的資料行。

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

### <a name="right-semi-join-flavor"></a>右側半聯結類別

右側半聯結會傳回右側中與左側記錄相符的所有記錄。 只會傳回右側的資料行。

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

Kusto 並未原生提供交叉聯結類別。 您無法使用 `kind=cross` 來標示運算子。
若要模擬，請使用虛擬索引鍵。

`X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy`

## <a name="join-hints"></a>聯結提示

`join` 運算子支援一些可控制查詢執行方式的提示。
這些提示不會變更 `join` 的語義，但可能會影響其效能。

下列文章會說明聯結提示：

* `hint.shufflekey=<key>` 和 `hint.strategy=shuffle` - [隨機查詢](shufflequery.md)
* `hint.strategy=broadcast` - [廣播聯結](broadcastjoin.md)
* `hint.remote=<strategy>` - [跨叢集聯結](joincrosscluster.md)
