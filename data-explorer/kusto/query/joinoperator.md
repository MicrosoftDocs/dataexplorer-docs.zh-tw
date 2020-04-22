---
title: 聯接運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的聯接運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 86d883c8d0214d278099260fa2406b91b776b4d1
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765822"
---
# <a name="join-operator"></a>join 運算子

透過比對每個資料表中所指定資料行的值，來合併兩個資料表的資料列，以形成新的資料表。

```kusto
Table1 | join (Table2) on CommonColumn, $left.Col1 == $right.Col2
```

**語法**

*左表*`|``join`=*聯接參數*= `(` *右表*`)``on`*屬性*

**引數**

* *左表*:要合併其行的**左**表或表格表達式(有時稱為**外部**表)。 表示為`$left`。

* *右表*:要合併其行**的右**表或表格表達式(有時稱為 =*內部*表)。 表示為`$right`。

* *屬性*:一個或多個(逗號分隔)規則,用於描述*左表*的行如何與*右表*的行匹配。 使用`and`邏輯運算符評估多個規則。
  規則可以是:

  |規則類型        |語法                                          |Predicate                                                      |
  |-----------------|------------------------------------------------|---------------------------------------------------------------|
  |依名稱相等 |*ColumnName*                                    |`where`*左表*。*欄位*`==`*右表*。*欄名稱*|
  |依值相等|`$left.`*左柱*`==``$right.`*右柱*|`where``$left.`*LeftColumn*左`==`柱`$right.`*右柱*       |

> [!NOTE]
> 在「按值相等」 的情況下,*欄位名稱必須*限定為由`$left`和`$right`符號表示的適用擁有者表。

* *聯接參數*:以*名稱*`=`*值*的形式控制行匹配操作和執行計劃的行為的零個或多個(空間分隔)參數。 支援下列參數： 

::: zone pivot="azuredataexplorer"

  |名稱           |值                                        |描述                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |聯結類別|請參閱[加入口味](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |請參閱[跨群集聯接](joincrosscluster.md)|
  |`hint.strategy`|執行提示                               |請參考[加入提示](#join-hints)                |

::: zone-end

::: zone pivot="azuremonitor"

  |名稱           |值                                        |描述                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`kind`         |聯結類別|請參閱[加入口味](#join-flavors)|                                             |
  |`hint.remote`  |`auto`, `left`, `local`, `right`              |                                             |
  |`hint.strategy`|執行提示                               |請參考[加入提示](#join-hints)                |

::: zone-end


> [!WARNING]
> 預設聯結樣式(如果`kind`未指定)`innerunique`為 。 這與其他分析產品不同,這些產品具有`inner`默認風格。 請閱讀[下文](#join-flavors),瞭解不同類型之間的差異,並確保查詢產生預期結果。

**傳回**

輸出架構取決於聯接風格:

 * `kind=leftanti`, `kind=leftsemi`:

     結果表僅包含左側的列。

     
 * `kind=rightanti`, `kind=rightsemi`:

     結果表僅包含右側的列。

     
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`

     兩個資料表中的每個資料行各自佔據一個資料行，包括用來比對的索引鍵。 如果名稱有衝突，右側的資料行會自動重新命名。

     
輸出紀錄取決於聯結風格:

 * `kind=leftanti`, `kind=leftantisemi`

     從左側返回右側沒有匹配項的所有記錄。     
     
 * `kind=rightanti`, `kind=rightantisemi`

     從右側返回左側沒有匹配項的所有記錄。  
      
*  `kind=innerunique`, `kind=inner`, `kind=leftouter`, `kind=rightouter`, `kind=fullouter`, `kind=leftsemi`, `kind=rightsemi`

    輸入資料表間的每個相符項目各自佔據一個資料列。 匹配是從一個表中選擇的行,該表對於具有以下約束的其他表中的所有`on`欄位具有相同的值:

   - `kind`未指定,`kind=innerunique`

    左側只會有一個資料列符合 `on` 索引鍵的每個值。 此資料列與右側資料列的每個相符項目都會在輸出中佔有一個資料列。
    
   - `kind=leftsemi`
   
    從左側返回右側具有匹配項的所有記錄。
    
   - `kind=rightsemi`
   
       從右側返回從左側具有匹配項的所有記錄。

   - `kind=inner`
 
    左側和右側相符資料列的每個組合都會在輸出中佔有一個資料列。

   - `kind=leftouter` (或 `kind=rightouter` 或 `kind=fullouter`)

    除了內部的相符項目，左側 (和/或右側) 的每個資料列即使不相符也會佔有一個資料列。 在此情況下，不相符的輸出資料格會包含 null。
    如果好幾個資料列在這些欄位具有相同的值，每一個組合都會佔有一個資料列。

 

**技巧**

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

[有關此範例的更多](./samples.md#activities)。

## <a name="join-flavors"></a>聯結類別

聯結運算子的確切類別是以某種關鍵字來指定。 從今天起,Kusto 支援聯接運算符的以下口味: 

|聯結種類|描述|
|--|--|
|[`innerunique`](#default-join-flavor)( 或預設值為空 )|左邊重複資料消除的內部聯結|
|[`inner`](#inner-join)|標準內部連線|
|[`leftouter`](#left-outer-join)|左方外部聯結|
|[`rightouter`](#right-outer-join)|右外聯結|
|[`fullouter`](#full-outer-join)|完整外部聯結|
|[`leftanti`](#left-anti-join)、[`anti`](#left-anti-join)或[`leftantisemi`](#left-anti-join)|左反聯接|
|[`rightanti`](#right-anti-join)或[`rightantisemi`](#right-anti-join)|右防聯接|
|[`leftsemi`](#left-semi-join)|左半聯接|
|[`rightsemi`](#right-semi-join)|右半聯接|

### <a name="inner-and-innerunique-join-operator-flavors"></a>內部和內部唯一聯接運算元口味

-    使用**內部聯接風格**時,輸出中將有一行用於從左至右匹配行的每個組合,而沒有左鍵重複數據消除。 輸出將是左右鍵的點菜產品。
    **內部聯結**範例 :

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
|1|瓦爾1.2|1|val1.3|
|1|val1.1|1|val1.3|
|1|瓦爾1.2|1|瓦爾1.4|
|1|val1.1|1|瓦爾1.4|

-    使用**內部唯一聯接風格**將從左側刪除鍵,並且輸出中將有一行來自重複數據消除的左鍵和右鍵的組合。
    上面使用的相同數據集**的內部唯一聯接**示例,請注意,此情況的內部**唯一風味**可能產生兩個可能的輸出,並且兩者都是正確的。
    在第一個輸出中,聯接運算符隨機選取在 t1 中顯示的第一個鍵,該鍵的值為"val1.1",並將其與 t2 鍵匹配;而在第二個輸出中,聯接運算符隨機選取第二個鍵出現在值為「val1.2」的 t1 中,並將其與 t2 鍵匹配:

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
|1|val1.1|1|瓦爾1.4|

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
|1|瓦爾1.2|1|val1.3|
|1|瓦爾1.2|1|瓦爾1.4|


-    Kusto 經過優化,可盡可能將後`join`方的濾波器推向相應的連接側左側或右側。
    有時,當使用的味道是**內部唯一**的,並且篩選器可以傳播到聯接的左側時,它將自動傳播,並且應用於該篩選器的鍵將始終出現在輸出中。
    例如,使用上面的範例並添加篩選器` where value == "val1.2" `將始終給出第二個結果,並且永遠不會為已使用的數據集提供第一個結果:

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
|1|瓦爾1.2|1|val1.3|
|1|瓦爾1.2|1|瓦爾1.4|
 
### <a name="default-join-flavor"></a>預設聯結風格
    
    X | join Y on Key
    X | join kind=innerunique Y on Key
     
讓我們使用兩個範例表來解釋聯接的操作: 
 
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

|Key|Value1|金鑰1|Value2|
|---|---|---|---|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|


(請注意,鍵"a"和"d"不會顯示在輸出中,因為左側和右側沒有匹配的鍵)。 
 
(從歷史上看,這是 Kusto 初始版本支援的聯接的第一個實現;在典型的日誌/跟蹤分析方案中非常有用,我們希望將兩個事件(每個事件匹配一個篩選條件)關聯在同一關聯 ID 下,並返回我們查找現象的所有外觀,而忽略貢獻跟蹤記錄的多個外觀。
 
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

|Key|Value1|金鑰1|Value2|
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

|Key|Value1|金鑰1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|a|1|||

 
### <a name="right-outer-join"></a>右外聯結 

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

|Key|Value1|金鑰1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|

 
### <a name="full-outer-join"></a>完整外部聯結 

就概念而言，完整外部聯結結合了套用左外部聯結與右外部聯結的效果。 如果聯接表中的記錄不匹配,則結果集將具有缺少匹配行的表的每個列的 NULL 值。 針對相符的記錄，會在結果集中產生單一的資料列 (包含從兩個資料表填入的資料行)。 
 
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

|Key|Value1|金鑰1|Value2|
|---|---|---|---|
|b|3|b|10|
|b|2|b|10|
|c|4|c|20|
|c|4|c|30|
|||d|40|
|a|1|||

 
### <a name="left-anti-join"></a>左反聯接

左反聯接返回左側與右側任何記錄不匹配的所有記錄。 
 
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

### <a name="right-anti-join"></a>右防聯接

右反聯接返回右側與左側任何記錄不匹配的所有記錄。 
 
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

### <a name="left-semi-join"></a>左半聯接

左半聯接返回左側與右側記錄匹配的所有記錄。 僅返回左側的列。 

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

### <a name="right-semi-join"></a>右半聯接

右半聯接從右側返回與左側記錄匹配的所有記錄。 僅返回右側的列。 

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

Kusto 不提供本地的交叉聯接風格(即,您無法`kind=cross`用 標記運算符)。
但是,通過提出一個虛擬密鑰來類比這一點並不難:

    X | extend dummy=1 | join kind=inner (Y | extend dummy=1) on dummy

## <a name="join-hints"></a>加入提示

運算子`join`支援控制查詢執行方式的一些提示。
這些不會更改`join`的語義,但可能會影響其性能。

加入以下文章中介紹的提示: 
* `hint.shufflekey=<key>`與`hint.strategy=shuffle` - [隨機查詢](shufflequery.md)
* `hint.strategy=broadcast` - [廣播聯結](broadcastjoin.md)
* `hint.remote=<strategy>` - [跨群集聯結](joincrosscluster.md)
