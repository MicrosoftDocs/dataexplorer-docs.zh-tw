---
title: KQL 快速參考
description: 實用 KQL 函式及其定義的清單，其中包含語法範例。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 3b007d1688130449c597ef99281ed89b55d880eb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95511919"
---
# <a name="kql-quick-reference"></a>KQL 快速參考

本文顯示函式及其描述的清單，以協助您開始使用 Kusto 查詢語言。

| 運算子/函式                               | 描述                           | 語法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**篩選/搜尋/條件**                      |**_藉由篩選或搜尋來尋找相關資料_** |                      |
| [where](kusto/query/whereoperator.md)                      | 依據特定述詞篩選           | `T | where Predicate`                         |
| [where contains/has](kusto/query/whereoperator.md)        | `Contains`：尋找任何符合的子字串 <br> `Has`：尋找特定字組 (效能較佳)  | `T | where col1 contains/has "[search term]"`|
| [search](kusto/query/searchoperator.md)                    | 在資料表中的所有資料行中搜尋值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [take](kusto/query/takeoperator.md)                        | 傳回指定的記錄筆數。 用來測試查詢<br>**_注意_**：`_take`_ 和 `_limit`_ 是同義字。 | `T | take NumberOfRows` |
| [case](kusto/query/casefunction.md)                        | 新增條件陳述式，類似於其他系統中的 if/then/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 產生資料表，其中以不同方式結合提供的輸入資料表資料列 | `distinct [ColumnName], [ColumnName]` |
| **日期/時間**                                   |**_使用日期和時間函式的作業_**               |                          |
|[ago](kusto/query/agofunction.md)                           | 傳回相對於查詢執行時間的時間位移。 例如，`ago(1h)` 是目前時鐘讀取的前一小時。 | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | 傳回[各種日期格式](kusto/query/format-datetimefunction.md#supported-formats)的資料。 | `format_datetime(datetime , format)` |
| [bin](kusto/query/binfunction.md)                          | 將時間範圍中的所有值進行四捨五入並予以分組 | `bin(value,roundTo)` |
| **建立/移除資料行**                   |**_新增或移除資料表中的資料行_** |                                                    |
| [print](kusto/query/printoperator.md)                      | 輸出含有一或多個純量運算式的單一資料列 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [project](kusto/query/projectoperator.md)                  | 選取要以指定的順序納入的資料行 | `T | project ColumnName [= Expression] [, ...]` <br> Or <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 選取要從輸出中排除的資料行 | `T | project-away ColumnNameOrPattern [, ...]` |
| [project-keep](kusto/query/project-keep-operator.md)         | 選取要保留在輸出中的資料行 | `T | project-keep ColumnNameOrPattern [, ...]` |
| [project-rename](kusto/query/projectrenameoperator.md)     | 將結果輸出中的資料行重新命名 | `T | project-rename new_column_name = column_name` |
| [project-reorder](kusto/query/projectreorderoperator.md)   | 將結果輸出中的資料行重新排序 | `T | project-reorder Col2, Col1, Col* asc` |
| [extend](kusto/query/extendoperator.md)                    | 建立計算結果欄並將其新增至結果集 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序和彙總資料集**                 |**_以有意義的方式將資料排序或分組以便進行重組_**|                  |
| [sort](kusto/query/sortoperator.md)                        | 以遞增或遞減順序，依一或多個資料行排序輸入資料表的資料列 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [top](kusto/query/topoperator.md)                          | 使用 `by` 排序資料集時，傳回資料集的前 N 個資料列 | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [summarize](kusto/query/summarizeoperator.md)              | 根據 `by` 群組資料行將資料列分組，並計算每個群組的彙總 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [計數](kusto/query/countoperator.md)                       | 計算輸入資料表中的記錄 (例如 T)<br>此運算子是 `summarize count() ` 的縮寫| `T | count` |
| [join](kusto/query/joinoperator.md)                        | 透過比對每個資料表中所指定資料行的值，來合併兩個資料表的資料列，以形成新的資料表。 支援全系列的聯結類型：`flouter`、`inner`、`innerunique`、`leftanti`、`leftantisemi`、`leftouter`、`leftsemi`、`rightanti`、`rightantisemi`、`rightouter`、`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [union](kusto/query/unionoperator.md)                      | 取得兩個或以上的資料表並傳回其所有資料列 | `[T1] | union [T2], [T3], …` |
| [range](kusto/query/rangeoperator.md)                      | 產生具有算術值數列的資料表 | `range columnName from start to stop step step` |
| **格式化資料**                                 | **_以實用的方式將資料重組為輸出_** | |
| [lookup](kusto/query/lookupoperator.md)                    | 使用維度資料表中所查閱的值，擴充事實資料表的資料行 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-expand](kusto/query/mvexpandoperator.md)               | 將動態陣列轉換成資料列 (多重值擴充) | `T | mv-expand Column` |
| [parse](kusto/query/parseoperator.md)                      | 評估字串運算式，並將其值剖析至一或多個計算的資料行。 用於將非結構化的資料結構化。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 沿著指定的軸建立一系列指定的彙總值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [let](kusto/query/letstatement.md)                         | 將名稱繫結至可參考其繫結值的運算式。 值可以是 lambda 運算式，以便在查詢中建立特殊函式。 使用 `let` 在結果看起來像新資料表的資料表上建立運算式。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **一般**                                     | **_其他作業和函式_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 在當作輸入接收的資料表上執行函式。 | `T | invoke function([param1, param2])` |
| [evaluate pluginName](kusto/query/evaluateoperator.md)     | 評估查詢語言擴充功能 (外掛程式) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覺效果**                               | **_以圖形格式顯示資料的作業_** | |
| [render](kusto/query/renderoperator.md) | 以圖形化輸出呈現結果 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
