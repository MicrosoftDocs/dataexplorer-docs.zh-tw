---
title: pattern 語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的模式語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1fa4c303624c62b7c43d2ddd0de58977ed6e42aa
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241346"
---
# <a name="pattern-statement"></a>pattern 語句

::: zone pivot="azuredataexplorer"

**模式**是名為 view 的結構，可將預先定義的字串元組對應到無參數函數主體。 模式在兩方面都是唯一的：

* 使用類似範圍表參考的語法來「叫用」模式。
* 模式具有一組可以對應的引數值，而且對應程式是由 Kusto 完成。 如果已宣告但未定義模式，Kusto 會識別並將模式的所有調用標記為錯誤。 此識別可讓中介層應用程式「解析」這些模式。

## <a name="pattern-declaration"></a>模式聲明

Pattern 語句用來宣告或定義模式。
例如，宣告為模式的模式語句 `app` 。

```kusto
declare pattern app;
```

這個語句會告知 Kusto `app` 是模式，但不會告訴 Kusto 如何解決模式。 如此一來，在查詢中叫用這個模式的任何嘗試都會產生特定的錯誤，並且會列出所有這類的調用。 例如：

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

此查詢會從 Kusto 產生錯誤，表示無法解析下一個模式調用： `app("ApplicationX")["StartEvents"]` 和 `app("ApplicationX")["StopEvents"]` 。

## <a name="syntax"></a>語法

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>模式定義

Pattern 語句也可以用來定義模式。 在模式定義中，會明確配置模式的所有可能調用，並提供對應的表格式運算式。 當 Kusto 接著執行查詢時，它會將每個模式調用取代為對應的模式主體。 例如：

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

針對每個符合的模式所提供的運算式，是資料表名稱或對 [let 語句](letstatement.md)的參考。

## <a name="syntax"></a>語法

`declare``pattern` *PatternName*  =  PatternName `(`*ArgName* `:`*ArgType* [ `,` ...] `)`[ `[` *PathName* `:` *PathArgType* `]` ]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [ `,` *ArgValue2* ...] `)` [ `.[` * PathValue `]` ] `=` `{` *運算式* `};` &nbsp; &nbsp; &nbsp; &nbsp; [ &nbsp; &nbsp; &nbsp; &nbsp; `(` *ArgValue1_2* [ `,` *ArgValue2_2* ...] `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; &nbsp; &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; ]        `}`

* *PatternName*： pattern 關鍵字的名稱。 只允許定義關鍵字的語法：用於偵測具有指定關鍵字的所有模式參考。
* *ArgName*：模式引數的名稱。 模式允許一個或多個引數名稱。
* *ArgType*：模式引數的類型 (目前只能 `string` 使用) 
* *PathName*：路徑引數的名稱。 模式允許零或一個路徑名稱。
* *PathType*： path 引數的類型 (目前只能 `string` 使用) 
* *ArgValue1*、 *ArgValue2*、...-pattern 引數的值 (目前只 `string` 允許常值) 
* *PathValue* -模式路徑的值 (目前只 `string` 允許常值) 
* *運算式*： *運算式* -表格式運算式 (例如， `Logs | where Timestamp > ago(1h)`) ，或參考函式的 lambda 運算式。

## <a name="pattern-invocation"></a>模式調用

模式調用語法類似于範圍表參考語法。

* *PatternName* `(`*ArgValue1* [ `,` *ArgValue2* ...] `).`*PathValue*
* *PatternName* `(`*ArgValue1* [ `,` *ArgValue2* ...] `).["`*PathValue*`"]`

## <a name="notes"></a>注意

**案例**

Pattern 語句是針對接受使用者查詢，然後將這些查詢傳送至 Kusto 的中介層應用程式所設計。 這類應用程式通常會在這些使用者查詢前面加上邏輯架構模型。 模型是一組 [let 語句](letstatement.md)，可能會以 [restrict 語句](restrictstatement.md)做為尾碼。

有些應用程式需要可提供使用者的語法。 語法是用來參考定義于應用程式所建立之邏輯架構中的實體。 不過，有時可能不會事先知道實體，或可能的實體數目太大而無法在邏輯架構中預先定義。

模式可透過下列方式解決此案例。 仲介層應用程式會將查詢傳送至 Kusto，並宣告所有已宣告但未定義的模式。 Kusto 接著會剖析查詢。 如果有一或多個模式調用，Kusto 會將錯誤傳回至中介層應用程式，並明確列出所有這類調用。 然後，中介層應用程式可以解析這些參考，然後重新執行查詢。 這次，請在前面加上完整詳細的模式定義。

**正規化**

Kusto 會自動標準化模式。 例如，以下是相同模式的所有調用，而且會回報一個單一模式。

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

這也表示您不能同時定義它們，因為它們被視為相同。

**萬用字元**

Kusto 不會以任何特殊方式處理模式中的萬用字元。 例如，在下列查詢中。

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto 將報告單一遺漏的模式調用： `app("ApplicationX").["*"]` 。

## <a name="examples"></a>範例

超過單一模式調用的查詢。

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|應用程式|SomeText|
|---|---|
|應用程式 #1|這是免費文字：1|
|應用程式 #1|這是免費文字：2|
|應用程式 #1|這是免費文字：3|
|應用程式 #1|這是免費文字：4|
|應用程式 #1|這是免費文字：5|
|應用程式 #2|這是免費文字：9|
|應用程式 #2|這是免費文字：8|
|應用程式 #2|這是免費文字：7|
|應用程式 #2|這是免費文字：6|
|應用程式 #2|這是免費文字：5|

```kusto
declare pattern App;
union (App('a1').Text), (App('a2').Text)
```

**語意錯誤**：

> SEM0036：未宣告一或多個模式參考。 偵測到的模式參考： ["App ( ' a1 ' ) . ['文字 '] "、" App ( ' a2 ' ) 。[' Text '] "]。

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

**傳回的語意錯誤**：

> SEM0036：未宣告一或多個模式參考。 偵測到的模式參考： ["App ( ' a2 ' ) . ['計量 '] "、" App ( ' a3 ' ) 。[' 度量 '] "]。

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
