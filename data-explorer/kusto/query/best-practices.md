---
title: 查詢最佳實務 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的查詢最佳實踐。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 3458c2fcd7fab3345f65393d7e9a13e844088a9f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663864"
---
# <a name="query-best-practices"></a>查詢最佳做法 

## <a name="general"></a>一般

有幾個"做和不做",你可以遵循,使你的查詢運行更快。

### <a name="do"></a>要做的事

*   先使用時間篩選器。 Kusto 經過高度優化,可利用時間篩檢程式。
*   使用字串運算子時:
    *   在`has`查找`contains`完整 令牌時,更喜歡運算符。 `has`性能更出色,因為它不必查找子字串。
    *   在適用時,更喜歡使用區分大小寫的運算符,因為它們更具有性能。 例如`==`,更喜歡使用`=~`而不是`in``in~`,`contains_cs``contains`而不是 , 而不是`contains`/`contains_cs`(`has`/`has_cs`但如果你能完全避免 和使用 ,那更好)。
*   更喜歡查看特定欄,而不是使用`*`(跨所有列進行全文搜尋)
*   如果您發現大多數查詢都涉及從數百萬行[的動態物件](./scalar-data-types/dynamic.md)中提取欄位,請考慮在引入時實現此列。 這樣,您只需支付一次列提取費用。  
*   如果您有一個`let`多次使用其值的語句,請考慮[materialize() function](./materializefunction.md)[best practices](#materialize-function)`materialize()`使用

### <a name="dont"></a>不要做的事

*   不要在沒有`limit [small number]``count`或 結束的情況下嘗試新的查詢。
    在未知數據集上運行未綁定查詢可能會導致將 GB 返回到客戶端的結果,從而導致回應速度慢且群集繁忙。
*   如果您發現應用的轉換(JSON、字串等)超過 10 億條記錄 - 重塑查詢以減少輸入轉換的資料量。
*   不要使用`tolower(Col) == "lowercasestring"`執行不區分大小寫的比較。 請改用 `Col =~ "lowercasestring"`。
    *   如果資料已採用小寫(或大寫),則避免使用不區分大小寫的比較,而是使用`Col == "lowercasestring"`(`Col == "UPPERCASESTRING"`或 )
*   如果可以在表列上進行篩選,則不要在計算列上進行篩選。 換句話說:`T | extend _value = <expression> | where predicate(_value)`而不是`T | where predicate(<expression>)`, 做 : .

## <a name="summarize-operator"></a>summarize 運算子

*   當匯總運算子的按鍵分組具有高基數(最佳做法:超過 100 萬)時,建議使用[提示.策略\隨機播放](./shufflequery.md)。

## <a name="join-operator"></a>join 運算子

*   使用[聯接運算符](./joinoperator.md)時,選擇行較少的表作為第一個(最左側)的表。 
*   跨群集使用[聯接運算符](./joinoperator.md)數據時,在聯接的「右側」(大多數數據所在的位置)上運行查詢。
*   當左側較小(最多 100,000 條記錄)且右側較大時,請使用[提示。](./broadcastjoin.md)
*   當聯接的兩側太大且聯接鍵具有高基數時,請使用[提示。策略\隨機播放](./shufflequery.md)。
    
## <a name="parse-operator-and-extract-function"></a>解析運算子和提取()函數

*   當目標列中的值包含所有共用相同格式或模式的字串時,[解析運算元](./parseoperator.md)(簡單模式)非常有用。
例如,對於具有值(如`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`)的列,在提取每個欄位的值時,請`parse`使用 運算符而不是`extract()`多個語句。
*   當解析的字串不都遵循相同的格式或模式時,[提取()函數](./extractfunction.md)非常有用。
在這種情況下,請使用 REGEX 提取所需的值。

## <a name="materialize-function"></a>具體函數

*   使用[具體()函數](./materializefunction.md)時,嘗試推送所有可能的運算符,以減少具體化數據集,並保留查詢的語義。 例如,篩選器或僅專案所需的列。
    
    **範例:**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* 文本上的篩選器是相互的,可以推送到具體運算式。
    查詢只需要`Timestamp`這些列`Text``Resource1``Resource2`, 因此建議在具體化運算式中投影這些列。
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   如果篩選器不相同,請使用此查詢:  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   可能值得(當組合篩選器大大減少具體化結果時),通過邏輯`or`表達式將兩個篩選器合併到具體結果上,如下所示。 但是,將篩選器保留在每個聯合段中,以保留查詢的語義:
     
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text has "String1" or Text has "String2"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```
    