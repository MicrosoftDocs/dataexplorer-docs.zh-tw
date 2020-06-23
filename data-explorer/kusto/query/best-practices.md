---
title: 查詢最佳做法-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的查詢最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e6db181188250d28689cca64762e13973f2f33f8
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128813"
---
# <a name="best-practices"></a>最佳作法 

## <a name="general"></a>一般

您可以遵循幾個「Dos 和不確定」，讓您更快速地執行查詢。

### <a name="do"></a>要做的事

*   先使用時間篩選器。 Kusto 已高度優化，可利用時間篩選準則。
*   使用字串運算子時：
    *   `has` `contains` 尋找完整權杖時偏好使用運算子。 `has`更具效能，因為它不需要查詢子字串。
    *   偏好使用區分大小寫的運算子（如果適用的話），因為它們的效能更佳。 例如，偏好使用 `==` over `=~` 、 `in` over `in~` 和 `contains_cs` over `contains` （但如果您可以避免 `contains` / `contains_cs` 全部使用， `has` / `has_cs` 這甚至更好）。
*   偏好查看特定的資料行，而不是使用 `*` （在所有資料行上進行全文檢索搜尋）
*   如果您發現大部分的查詢都是從[動態物件](./scalar-data-types/dynamic.md)跨越數百萬個數據列來提取欄位，請考慮在內嵌時將此資料行具體化。 如此一來，您只需要針對資料行解壓縮付費一次。  
*   如果您的 `let` 語句所使用的值超過一次，請考慮使用[具體化（）](./materializefunction.md)函式（請參閱一些有關使用的[最佳做法](#materialize-function) `materialize()` ）。

### <a name="dont"></a>禁止事項

*   不要在結尾處嘗試新的查詢 `limit [small number]` `count` 。
    在未知的資料集上執行未系結的查詢，可能會產生傳回給用戶端的 Gb 結果，因而導致回應緩慢和叢集忙碌中。
*   如果您發現您要套用超過1000000000記錄的轉換（JSON、字串等），請調整您的查詢，以減少送到轉換的資料量。
*   請勿使用 `tolower(Col) == "lowercasestring"` 來執行不區分大小寫的比較。 請改用 `Col =~ "lowercasestring"`。
    *   如果您的資料已經是小寫（或大寫），請避免使用不區分大小寫的比較，並改為使用 `Col == "lowercasestring"` （或 `Col == "UPPERCASESTRING"` ）。
*   如果您可以篩選資料表資料行，請勿篩選計算結果欄。 換句話說：而不是 `T | extend _value = <expression> | where predicate(_value)` ，do： `T | where predicate(<expression>)` 。

## <a name="summarize-operator"></a>summarize 運算子

*   當摘要運算子的 group by 索引鍵為高基數（最佳作法：高於1000000）時，建議使用[提示。策略 = 隨機播放](./shufflequery.md)。

## <a name="join-operator"></a>join 運算子

*   使用[join 運算子](./joinoperator.md)時，請選取資料列較少的資料表（最左邊）。 
*   在叢集上使用[聯結運算子](./joinoperator.md)資料時，請在聯結的「右側」端執行查詢（大部分的資料位於其中）。
*   當左邊是小的（最多100000筆記錄）且右邊很大時，請使用[提示。策略 = 廣播](./broadcastjoin.md)。
*   當聯結的兩端都太大，且聯結索引鍵具有高基數時，請使用[提示。策略 = 隨機播放](./shufflequery.md)。
    
## <a name="parse-operator-and-extract-function"></a>parse 運算子和摘錄（）函數

*   當目標資料行中的值包含所有共用相同格式或模式的字串時， [parse 運算子](./parseoperator.md)（簡單模式）就很有用。
例如，對於具有類似值的資料行， `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` 在提取每個欄位的值時，請使用 `parse` 運算子，而不是數個 `extract()` 語句。
*   當剖析的字串不是全部遵循相同的格式或模式時，[解壓縮（）函數](./extractfunction.md)就很有用。
在這種情況下，請使用 REGEX 來解壓縮必要的值。

## <a name="materialize-function"></a>具體化（）函數

*   使用[具體化（）函數](./materializefunction.md)時，請嘗試推送所有可能會減少具體化資料集的運算子，並繼續保留查詢的語法。 例如，篩選或僅專案必要的資料行。
    
    **範例︰**

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource1)), (materializedData
    | where Text !has "somestring"
    | summarize dcount(Resource2))
    ```

* 文字上的篩選是相互的，而且可以推送至具體化運算式。
    查詢只需要這些資料行 `Timestamp` 、 `Text` `Resource1` 和， `Resource2` 因此建議您在具體化運算式內投影這些資料行。
    
    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d)
    | where Text !has "somestring"
    | project Timestamp, Resource1, Resource2, Text);
    union (materializedData
    | summarize dcount(Resource1)), (materializedData
    | summarize dcount(Resource2))
    ```
    
*   如果篩選與在此查詢中的內容不同：  

    ```kusto
    let materializedData = materialize(Table
    | where Timestamp > ago(1d));
    union (materializedData
    | where Text has "String1"
    | summarize dcount(Resource1)), (materializedData
    | where Text has "String2"
    | summarize dcount(Resource2))
    ```

*   這可能是值得的（當結合的篩選準則大幅減少具體化的結果時），會依照下列查詢中的邏輯運算式，將具體化結果的兩個篩選器結合在一起 `or` 。 不過，請在每個聯集階段中保留篩選，以保留查詢的語義：
     
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
    