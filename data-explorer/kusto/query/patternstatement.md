---
title: pattern 語句-Azure 資料總管
description: 本文說明 Azure 資料總管中的模式語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a4aae88f6ad435469719f8444bae9123975ee618
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346213"
---
# <a name="pattern-statement"></a>pattern 語句

::: zone pivot="azuredataexplorer"

**模式**是名為類似視圖的結構，可將預先定義的字串元組對應至無參數函式主體。 模式在兩個層面中都是唯一的：

* 使用類似範圍資料表參考的語法來「叫用」模式。
* 模式具有受控制、封閉式、可對應的引數值集，而對應程式則是由 Kusto 完成。 如果已宣告模式但未定義，Kusto 會識別並將所有叫用的模式標記為錯誤。 此識別可以讓仲介層應用程式「解析」這些模式。

## <a name="pattern-declaration"></a>模式聲明

Pattern 語句是用來宣告或定義模式。
例如，宣告為模式的模式語句 `app` 。

```kusto
declare pattern app;
```

此語句會告訴 Kusto `app` 是模式，但不會告訴 Kusto 如何解析模式。 因此，在查詢中叫用此模式的任何嘗試都會導致特定的錯誤，而且會列出所有這類調用。 例如：

```kusto
declare pattern app;
app("ApplicationX").StartEvents
| join kind=inner app("ApplicationX").StopEvents on CorrelationId
| count
```

此查詢將會從 Kusto 產生錯誤，指出無法解析下一個模式調用： `app("ApplicationX")["StartEvents"]` 和 `app("ApplicationX")["StopEvents"]` 。

## <a name="syntax"></a>語法

`declare``pattern` *PatternName*

## <a name="pattern-definition"></a>模式定義

Pattern 語句也可以用來定義模式。 在模式定義中，會明確配置模式的所有可能調用，並提供對應的表格式運算式。 當 Kusto 然後執行查詢時，它會以對應的模式主體取代每個模式調用。 例如：

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

針對每個符合的模式所提供的運算式是資料表名稱或[let 語句](letstatement.md)的參考。

## <a name="syntax"></a>語法

`declare``pattern` *PatternName*  =  PatternName `(`*ArgName* `:`*ArgType* [ `,` ...] `)`[ `[` *路徑名稱* `:` *PathArgType* `]` ]`{`
&nbsp;&nbsp;&nbsp;&nbsp;`(` *ArgValue1* [ `,` *ArgValue2* ...] `)` [ `.[` * PathValue `]` ] `=` `{` *運算式* `};` &nbsp; &nbsp; &nbsp; &nbsp; [ &nbsp; &nbsp; &nbsp; &nbsp; `(` *ArgValue1_2* [ `,` *ArgValue2_2* ...] `)` [ `.[` *PathValue_2* `]` ] `=` `{` *expression_2* `};` &nbsp; &nbsp; &nbsp; &nbsp; ... &nbsp; &nbsp; &nbsp; &nbsp; ]        `}`

* *PatternName*： pattern 關鍵字的名稱。 只允許定義關鍵字的語法：使用指定的關鍵字偵測所有模式參考。
* *ArgName*： pattern 引數的名稱。 模式允許一個或多個引數名稱。
* *ArgType*： pattern 引數的類型（目前只 `string` 允許）
* *PathName*： path 引數的名稱。 模式允許零或一個路徑名稱。
* *PathType*： path 引數的類型（目前只 `string` 允許）
* *ArgValue1*， *ArgValue2*，...-pattern 引數的值（目前只 `string` 允許常值）
* *PathValue* -模式路徑的值（目前只 `string` 允許常值）
* *expression*：*運算式*-表格式運算式（例如， `Logs | where Timestamp > ago(1h)` ），或參考函數的 lambda 運算式。

## <a name="pattern-invocation"></a>模式調用

模式調用語法類似于範圍資料表參考語法。

* *PatternName* `(`*ArgValue1* [ `,` *ArgValue2* ...] `).`*PathValue*
* *PatternName* `(`*ArgValue1* [ `,` *ArgValue2* ...] `).["`*PathValue*`"]`

## <a name="notes"></a>注意

**案例**

Pattern 語句是針對接受使用者查詢，然後將這些查詢傳送至 Kusto 的中介層應用程式所設計。 這類應用程式通常會在這些使用者查詢前面加上邏輯架構模型。 模型是一組[let 語句](letstatement.md)，可能會以[restrict 語句](restrictstatement.md)作為尾碼。

有些應用程式需要可提供使用者的語法。 語法是用來參考在應用程式所結構的邏輯架構中所定義的實體。 不過，有時不會事先知道實體，或可能的實體數目太大而無法在邏輯架構中預先定義。

模式會以下列方式解決此案例。 中介層應用程式會將查詢傳送至 Kusto，並宣告所有模式，但未定義。 Kusto 接著會剖析查詢。 如果有一或多個模式叫用，Kusto 會將錯誤傳回至中介層應用程式，並明確列出所有此類調用。 中介層應用程式接著可以解析每個參考，然後重新執行查詢。 這一次，請在其前面加上完整的詳細模式定義。

**正規化**

Kusto 會自動標準化模式。 例如，下列是相同模式的所有調用，而且會回報單一。

```kusto
declare pattern app;
union
  app("ApplicationX").StartEvent,
  app('ApplicationX').StartEvent,
  app("ApplicationX").['StartEvent'],
  app("ApplicationX").["StartEvent"]
```

這也表示您無法將它們定義在一起，因為它們被視為相同。

**萬用字元**

Kusto 不會以任何特殊方式將萬用字元視為模式。 例如，在下列查詢中。

```kusto
declare pattern app;
union app("ApplicationX").*
| count
```

Kusto 會報告單一遺漏的模式調用： `app("ApplicationX").["*"]` 。

## <a name="examples"></a>範例

查詢超過單一模式調用。

```kusto
declare pattern A
{
    // ...
};
union (A('a1').Text), (A('a2').Text)
```

|App|SomeText|
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

     SEM0036: One or more pattern references weren't declared. Detected pattern references: ["App('a1').['Text']","App('a2').['Text']"].

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

    SEM0036: One or more pattern references weren't declared. Detected pattern references: ["App('a2').['Metrics']","App('a3').['Metrics']"].

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援這項功能

::: zone-end
