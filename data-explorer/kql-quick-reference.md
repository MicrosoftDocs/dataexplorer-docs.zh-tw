---
title: KQL 快速參考
description: 有用的 KQL 函式清單及其定義和語法範例。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 2fa4cbd0b1cf7b034bc7ae3202afcde3866ca347
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356583"
---
# <a name="kql-quick-reference"></a>KQL 快速參考

本文將說明函式清單及其描述，以協助您開始使用 Kusto 查詢語言。

| Operator/函式                               | 說明                           | 語法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**篩選準則/搜尋/條件**                      |**_藉由篩選或搜尋來尋找相關資料_** |                      |
| [where](kusto/query/whereoperator.md)                      | 特定述詞上的篩選準則           | `T | where Predicate`                         |
| [where contains/contains](kusto/query/whereoperator.md)        | `Contains`：尋找是否有任何子字串相符 <br> `Has`：尋找特定字組 (效能較佳)   | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | 搜尋資料表中的所有資料行中的值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | 傳回指定的記錄數目。 用來測試查詢<br>**_注意_**： `_take` _ 和 `_limit` _ 為同義字。 | `T | take NumberOfRows` |
| [情況 下](kusto/query/casefunction.md)                        | 加入 condition 語句，類似于其他系統中的 if/then/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 產生資料表，其中以不同方式結合提供的輸入資料表資料列 | `distinct [ColumnName], [ColumnName]` |
| **日期/時間**                                   |**_使用日期和時間函數的作業_**               |                          |
|[前](kusto/query/agofunction.md)                           | 傳回相對於查詢執行時間的時間位移。 例如， `ago(1h)` 是目前時鐘讀取之前的一小時。 | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | 傳回 [各種日期格式](kusto/query/format-datetimefunction.md#supported-formats)的資料。 | `format_datetime(datetime , format)` |
| [站](kusto/query/binfunction.md)                          | 四捨五入時間範圍內的所有值，並將其分組 | `bin(value,roundTo)` |
| **建立/移除資料行**                   |**_在資料表中新增或移除資料行_** |                                                    |
| [print](kusto/query/printoperator.md)                      | 輸出具有一或多個純量運算式的單一資料列 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | 選取要包含在指定順序中的資料行 | `T | project ColumnName [= Expression] [, ...]` <br> Or <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 選取要從輸出中排除的資料行 | `T | project-away ColumnNameOrPattern [, ...]` |
| [專案-保留](kusto/query/project-keep-operator.md)         | 選取要保留在輸出中的資料行 | `T | project-keep ColumnNameOrPattern [, ...]` |
| [專案-重新命名](kusto/query/projectrenameoperator.md)     | 重新命名結果輸出中的資料行 | `T | project-rename new_column_name = column_name` |
| [專案-重新排序](kusto/query/projectreorderoperator.md)   | 重新排序結果輸出中的資料行 | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | 建立匯出資料行，並將其加入至結果集。 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序和匯總資料集**                 |**_以有意義的方式排序或分組來重建資料_**|                  |
| [sort](kusto/query/sortoperator.md)                        | 依一或多個資料行以遞增或遞減順序排序輸入資料表的資料列 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | 使用排序資料集時，傳回資料集的前 N 個數據列 `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | 根據群組資料行將資料 `by` 列分組，並計算每個群組的匯總 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [計數](kusto/query/countoperator.md)                       | 計算輸入資料表中的記錄 (例如 T) <br>這個運算子是的縮寫 `summarize count() `| `T | count` |
| [join](kusto/query/joinoperator.md)                        | 將兩個數據表的資料列合併，藉由比對每個資料表中指定之資料行 () s 的值，來形成新的資料表。 支援完整範圍的聯結類型： `flouter` 、 `inner` 、 `innerunique` 、 `leftanti` 、 `leftantisemi` 、 `leftouter` 、 `leftsemi` `rightanti` `rightantisemi` `rightouter` 、、、、 `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | 採用兩個或多個資料表，並傳回其所有資料列 | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | 產生具有算術值系列的資料表 | `range columnName from start to stop step step` |
| **格式化資料**                                 | **_以實用的方式將資料重建為輸出_** | |
| [查找](kusto/query/lookupoperator.md)                    | 擴充事實資料表的資料行，並在維度資料表中查閱值 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               |  (多重值擴充將動態陣列轉換成資料列)  | `T | mv-expand Column` |
| [解析](kusto/query/parseoperator.md)                      | 評估字串運算式，並將其值剖析至一或多個計算的資料行。 用於結構化資料的結構。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 沿著指定的軸建立一系列指定的匯總值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [讓](kusto/query/letstatement.md)                         | 將名稱系結至可參考其系結值的運算式。 值可以是 lambda 運算式，以便在查詢中建立特定函數。 用 `let` 來建立資料表的運算式，而資料表的結果看起來就像新的資料表。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **一般**                                     | **_其他作業和函式_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 在其接收為輸入的資料表上執行函數。 | `T | invoke function([param1, param2])` |
| [評估 pluginName](kusto/query/evaluateoperator.md)     | 評估 (外掛程式) 的查詢語言延伸模組 | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覺效果**                               | **_以圖形格式顯示資料的作業_** | |
| [轉譯](kusto/query/renderoperator.md) | 以圖形化輸出呈現結果 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
