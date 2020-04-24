---
title: KQL 快速參考
description: 有用的 KQL 函數及其定義的清單,包含語法示例。
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: ff9b78af54141f2c7fdbbf7039aad59dca2312a0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500706"
---
# <a name="kql-quick-reference"></a>KQL 快速參考

本文將列出函數及其說明清單,以説明您開始使用 Kusto 查詢語言。

| 操作員/功能                               | 描述                           | 語法                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**過濾器/搜尋/條件**                      |**_以篩選或搜尋查詢相關資料_** |                      |
| [其中](kusto/query/whereoperator.md)                      | 特定謂詞上的篩選器           | `T | where Predicate`                         |
| [其中包含/有](kusto/query/whereoperator.md)        | `Contains`: 尋找任何子字串 <br> `Has`:尋找特定單字(效能更好)  | `T | where col1 contains/has "[search term]"`|
| [搜尋](kusto/query/searchoperator.md)                    | 搜尋表中的所有欄位以搜尋值 | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [採取](kusto/query/takeoperator.md)                        | 返回指定的記錄數。 測試查詢<br>**_注意_**`_take``_limit`: 和 * 是同義詞。 | `T | take NumberOfRows` |
| [情況 下](kusto/query/casefunction.md)                        | 添加條件語句,類似於其他系統中的 if/然後/elseif。 | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](kusto/query/distinctoperator.md)                | 產生有輸入表的使用者的不同組合的表格 | `distinct [ColumnName], [ColumnName]` |
| **日期/時間**                                   |**_使用日期與時間函數的操作_**               |                          |
|[前](kusto/query/agofunction.md)                           | 返回相對於查詢執行時間的時間偏移量。 例如,`ago(1h)`是目前時鐘讀數前一小時。 | `ago(a_timespan)` |
| [format_datetime](kusto/query/format-datetimefunction.md)  | 傳[回 各種日期格式](kusto/query/format-datetimefunction.md#supported-formats)的資料。 | `format_datetime(datetime , format)` |
| [站](kusto/query/binfunction.md)                          | 在時間範圍內捨入所有值並對其進行分組 | `bin(value,roundTo)` |
| **建立/刪除欄位**                   |**_新增或移除表中的欄位_** |                                                    |
| [列印](kusto/query/printoperator.md)                      | 使用一個或多個標量表示式輸出單個行 | `print [ColumnName =] ScalarExpression [',' ...]` |
| [專案](kusto/query/projectoperator.md)                  | 選擇要依指定順序的欄位 | `T | project ColumnName [= Expression] [, ...]` <br> Or <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [project-away](kusto/query/projectawayoperator.md)         | 選擇要從輸出中排除的欄位 | `T | project-away ColumnNameOrPattern [, ...]` |
| [延伸](kusto/query/extendoperator.md)                    | 建立計算欄位並將新增到結果集 | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **排序與集合資料集**                 |**_通過以有意義的方式對資料進行排序或分組來重組資料_**|                  |
| [排序](kusto/query/sortoperator.md)                        | 依遞增或遞減的順序順序, 以順序 | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [返回頁首](kusto/query/topoperator.md)                          | 使用資料集排序時傳回資料集的前 N 行`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [總結](kusto/query/summarizeoperator.md)              | 根據群組列對行進行`by`群組,並計算每個組的聚合 | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [計數](kusto/query/countoperator.md)                       | 計算輸入表格記錄(例如,T)<br>此運算子是速記`summarize count() `| `T | count` |
| [加入](kusto/query/joinoperator.md)                        | 通過匹配每個表中指定列的值,合併兩個表的行以形成新表。 支援各種`flouter`聯接類型: `inner` `innerunique`、 `leftanti` `leftantisemi` `leftouter` `leftsemi`、 `rightanti` `rightantisemi` `rightouter`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [聯盟](kusto/query/unionoperator.md)                      | 取得兩個或多個表並傳回所有行 | `[T1] | union [T2], [T3], …` |
| [範圍](kusto/query/rangeoperator.md)                      | 產生具有數字序列的表格 | `range columnName from start to stop step step` |
| **將資料格式化**                                 | **_以有用的方式重組資料以輸出_** | |
| [尋找](kusto/query/lookupoperator.md)                    | 以維度表中使用拾值延伸事實資料表的欄 | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv 延伸](kusto/query/mvexpandoperator.md)               | 將動態陣列轉換為行(多值延伸) | `T | mv-expand Column` |
| [解析](kusto/query/parseoperator.md)                      | 評估字串運算式，並將其值剖析至一或多個計算的資料行。 用於構建非結構化數據。 | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](kusto/query/make-seriesoperator.md)          | 沿指定軸建立一系列指定的集合值 | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [讓](kusto/query/letstatement.md)                         | 將名稱綁定到可以引用其綁定值的運算式。 值可以是 lambda 運算式,以創建作為查詢的一部分的臨時函數。 用於`let`在結果看起來像新表的表上創建運算式。 | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **一般**                                     | **_雜項操作和職能_** | |
| [invoke](kusto/query/invokeoperator.md)                    | 在作為輸入接收的表上運行函數。 | `T | invoke function([param1, param2])` |
| [評估外掛程式名稱](kusto/query/evaluateoperator.md)     | 評估查詢語言延伸(外掛程式) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **視覺效果**                               | **_以圖形格式顯示資料的操作_** | |
| [呈現](kusto/query/renderoperator.md) | 成像為圖形輸出 | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
