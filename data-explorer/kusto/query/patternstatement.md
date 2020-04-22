---
title: 模式語句 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的模式語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d7ad021fe7b458d54beeb24b908420324b45ad39
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765659"
---
# <a name="pattern-statement"></a>模式語句

::: zone pivot="azuredataexplorer"

**模式**是一個命名的檢視狀建構,用於將預定義的字串元數映射到無參數函數體。 模式在兩個方面是獨一無二的:

* 通過使用類似於作用域表引用的語法來「調用」模式。
* 模式具有一組可以映射的受控、端端的參數值,映射過程由 Kusto 完成。 這意味著,如果模式聲明但未定義,Kusto 會識別所有調用該模式並將其標記為錯誤,從而可以通過中間層應用程式"解析"這些模式。


## <a name="pattern-declaration"></a>模式宣告
模式語句用於聲明或定義模式。
例如,以下是宣告為模式的模式`app`語句:

```kusto
declare pattern app;
```

此語句告訴 Kusto`app`這是一個模式,但不會告訴 Kusto 如何解決該模式。 因此,在查詢中調用此模式的任何嘗試都將導致列出所有此類調用的特定錯誤。 例如：

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

此查詢將從 Kusto 產生錯誤,指示無法解決以下模式呼`app("ApplicationX")["StartEvents"]`叫`app("ApplicationX")["StopEvents"]`: 與 。

**語法**

`declare``pattern`*樣式名稱*

## <a name="pattern-definition"></a>模式定義

模式語句還可用於定義模式。 在模式定義中,顯式佈局模式的所有可能調用,並給出相應的表格運算式。 然後,當 Kusto 執行查詢時,它將每個模式調用替換為相應的模式正文。 例如：

```kusto
declare pattern app = (applicationId:string)[eventType:string]
{
    ("ApplicationX").["StopEvents"] = { database("AppX").Events | where EventType == "StopEvent" };
    ("ApplicationX").["StartEvents"] = { database(applicationId).Events | where EventType == eventType } ;
};
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

為要匹配的每個模式提供的表達式是表名或對[let 語句](letstatement.md)的引用。

**語法**

`declare``pattern`*樣式* = `(`名稱*ArgName* `:` *ArgType* =`,` ...`)` 【`[` *路徑名稱*`:`*路徑型態*`]`】`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* `,` = *ArgValue2* ...`)` 【`.[`路徑`]``=``{`值&nbsp;】*表示式*`};`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`(`[ *ArgValue1_2* ArgValue1_2 [ ArgValue2_2 ... &nbsp; &nbsp;`,`*ArgValue2_2*`)` [ `.[` `=` *expression_2* &nbsp; PathValue_2 `{` &nbsp; ] `};` expression_2 &nbsp; ... &nbsp; *PathValue_2* `]`&nbsp;&nbsp;&nbsp;&nbsp; ]        `}`

* *模式名稱*:模式關鍵字的名稱。 只允許定義關鍵字的語法:用於使用指定的關鍵字檢測所有模式引用。
* *ArgName*: 模式參數的名稱。 模式允許一個或多個參數名稱。
* *ArgType*: 模式參數的類型(`string`目前只有允許)
* *路徑名稱*:路徑參數的名稱。 模式允許零或一個路徑名稱。
* *路徑型態*:路徑參數的類型(`string`目前只有允許)
* *ArgValue1*, *ArgValue2*, ...`string`- 模式參數的值 (目前只允許文字)
* *路徑值*- 模式路徑的值 (`string`目前只允許文字)
* *表示式*:*表示式*- 表格表示式`Logs | where Timestamp > ago(1h)`(例如), 或引用函數的 lambda 運算式。

## <a name="pattern-invocation"></a>模式呼叫

模式呼叫語法以作用網域表引言語法:

* *模式名稱*`(` *ArgValue1* =`,` *ArgValue2* ...]`).`*路徑值*
* *模式名稱*`(` *ArgValue1* =`,` *ArgValue2* ...]`).["`*路徑值*`"]`

## <a name="remarks"></a>備註

**案例**

模式語句專為接受使用者查詢然後將這些查詢發送到 Kusto 的中間層應用程式而設計。 此類應用程式通常用邏輯架構模型(一組[let 語句](letstatement.md),可能由[限制語句](restrictstatement.md)後綴)來為這些使用者查詢提供前綴。
在某些情況下,這些應用程式需要一個語法,它們可以為使用者提供引用無法提前知道並在它們構造的邏輯架構中定義的實體(要麼是因為它們不是提前知道的,因為潛在實體的數量太大,無法在邏輯架構中預定義)。

模式以下列方式解決此方案。 中介層應用程式將查詢發送到 Kusto,所有模式都聲明,但未定義。 然後,Kusto 解析查詢,如果其中存在一個或多個模式調用,則會將錯誤返回到中間層應用程式,並顯式列出所有此類調用。 然後,中間層應用程式可以解決每個引用,然後重新運行查詢,這次使用完全詳細的模式定義來首碼查詢。

**規範化**

Kusto 會自動規範化模式,因此例如,以下是同一模式的所有呼叫,並且將報告回單個呼叫:

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

這也意味著不能將它們一起定義,因為它們被認為是相同的。

**萬用字元**

Kusto 不會以任何特殊的方式以任何特殊的方式處理通配符。 例如,在以下查詢中:

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto 將報告單個缺少的模式呼叫`app("ApplicationX").["*"]`: 。

## <a name="examples"></a>範例

查詢多個模式呼叫:

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|App|一些文字|
|---|---|
|應用#1|這是免費文字: 1|
|應用#1|這是免費文字: 2|
|應用#1|這是免費文字: 3|
|應用#1|這是免費文字: 4|
|應用#1|這是免費文字: 5|
|應用#2|這是免費文字: 9|
|應用#2|這是免費文字: 8|
|應用#2|這是免費文字: 7|
|應用#2|這是免費文字: 6|
|應用#2|這是免費文字: 5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

語意錯誤:

     SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

```kusto
declare pattern App;
declare pattern App = (applicationId:string)[scope:string]  
{
    ('a1').['Data']    = { range x from 1 to 5 step 1 | project App = "App #1", Data    = x };
    ('a1').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #1", Metrics = rand() };
    ('a2').['Data']    = { range x from 1 to 5 step 1 | project App = "App #2", Data    = 10 - x };
    ('a3').['Metrics'] = { range x from 1 to 5 step 1 | project App = "App #3", Metrics = rand() };
};
union (App('a2').Metrics), (App('a3').Metrics) 
```

傳回的語意錯誤:

    SEM0036: One or more pattern references were not declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
