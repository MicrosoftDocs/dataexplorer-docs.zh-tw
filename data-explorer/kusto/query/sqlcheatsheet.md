---
title: SQL 對 Kusto 查詢轉譯 - Azure Data Explorer
description: 本文說明 Azure 資料總管中的 SQL 對 Kusto 查詢轉譯。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.localizationpriority: high
ms.openlocfilehash: 16a0c6ef7064ae5e16fb649f3cd089196533ec45
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513364"
---
# <a name="sql-to-kusto-cheat-sheet"></a>SQL 對 Kusto 的功能提要

Kusto 支援 SQL 語言的子集。 如需不支援功能的完整清單，請參閱 [SQL 已知問題](../api/tds/sqlknownissues.md)的清單。

與 Kusto 互動的主要語言是 KQL (Kusto 查詢語言)。 若要讓轉換和學習體驗變得更容易，您可以使用 Kusto 將 SQL 查詢轉譯為 KQL。 將 SQL 查詢傳送至 Kusto，並在前面加上動詞 'EXPLAIN'。

例如：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
EXPLAIN 
SELECT COUNT_BIG(*) as C FROM StormEvents 
```

|查詢|
|---|
|StormEvents<br>| summarize C=count()<br>| project C|

## <a name="sql-to-kusto-cheat-sheet"></a>SQL 對 Kusto 的功能提要

下表顯示 SQL 中的範例查詢及其 KQL 對等項目。

|類別 |SQL 查詢 |Kusto 查詢
|---|---|---
從資料表選取資料 |<code>SELECT * FROM dependencies</code> | <code>dependencies</code>
--|<code>SELECT name, resultCode FROM dependencies</code> |<code>dependencies &#124; project name, resultCode</code>
--|<code>SELECT TOP 100 * FROM dependencies</code> | <code>dependencies &#124; take 100</code>
Null 評估 |<code>SELECT * FROM dependencies<br>WHERE resultCode IS NOT NULL</code> | <code>dependencies<br>&#124; where isnotnull(resultCode)</code>
比較運算子 (日期) |<code>SELECT * FROM dependencies<br>WHERE timestamp > getdate()-1</code>| <code>dependencies<br>&#124; where timestamp > ago(1d)</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp BETWEEN ... AND ...</code> |<code>dependencies<br>&#124; where timestamp > datetime(2016-10-01)<br>&nbsp;&nbsp;and timestamp <= datetime(2016-11-01)</code>
比較運算子 (字串)|<code>SELECT * FROM dependencies<br>WHERE type = "Azure blob"</code> |<code>dependencies<br>&#124; where type == "Azure blob"</code>
--|<code>-- substring<br>SELECT * FROM dependencies<br>WHERE type like "%blob%"</code> |<code>// substring<br>dependencies<br>&#124; where type contains "blob"</code>
--|<code>-- wildcard<br>SELECT * FROM dependencies<br>WHERE type like "Azure%"</code> |<code>// wildcard<br>dependencies<br>&#124; where type startswith "Azure"<br>// or<br>dependencies<br>&#124; where type matches regex "^Azure.*"</code>
比較 (布林值) |<code>SELECT * FROM dependencies<br>WHERE !(success)</code> |<code>dependencies<br>&#124; where success == "False"</code>
Distinct |<code>SELECT DISTINCT name, type  FROM dependencies</code> |<code>dependencies<br>&#124; summarize by name, type</code>
群組，彙總 |<code>SELECT name, AVG(duration) FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize avg(duration) by name</code>
資料行別名、擴充 |<code>SELECT operationName as Name, AVG(duration) as AvgD FROM dependencies<br>GROUP BY name</code> |<code>dependencies<br>&#124; summarize AvgD = avg(duration) by Name=operationName</code>
排序 |<code>SELECT name, timestamp FROM dependencies<br>ORDER BY timestamp ASC</code> |<code>dependencies<br>&#124; project name, timestamp<br>&#124; order by timestamp asc nulls last</code>
依量值的前 n 個 |<code>SELECT TOP 100 name, COUNT(*) as Count FROM dependencies<br>GROUP BY name<br>ORDER BY Count DESC</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; top 100 by Count desc</code>
Union |<code>SELECT * FROM dependencies<br>UNION<br>SELECT * FROM exceptions</code> |<code>union dependencies, exceptions</code>
--|<code>SELECT * FROM dependencies<br>WHERE timestamp > ...<br>UNION<br>SELECT * FROM exceptions<br>WHERE timestamp > ...</code> |<code>dependencies<br>&#124; where timestamp > ago(1d)<br>&#124; union<br>&nbsp;&nbsp;(exceptions<br>&nbsp;&nbsp;&#124; where timestamp > ago(1d))</code>
Join |<code>SELECT * FROM dependencies <br>LEFT OUTER JOIN exception<br>ON dependencies.operation_Id = exceptions.operation_Id</code> |<code>dependencies<br>&#124; join kind = leftouter<br>&nbsp;&nbsp;(exceptions)<br>on $left.operation_Id == $right.operation_Id</code>
巢狀查詢 |<code>SELECT * FROM dependencies<br>WHERE resultCode == <br>(SELECT TOP 1 resultCode FROM dependencies<br>WHERE resultId = 7<br>ORDER BY timestamp DESC)</code> |<code>dependencies<br>&#124; where resultCode == toscalar(<br>&nbsp;&nbsp;dependencies<br>&nbsp;&nbsp;&#124; where resultId == 7<br>&nbsp;&nbsp;&#124; top 1 by timestamp desc<br>&nbsp;&nbsp;&#124; project resultCode)</code>
Having |<code>SELECT COUNT(\*) FROM dependencies<br>GROUP BY name<br>HAVING COUNT(\*) > 3</code> |<code>dependencies<br>&#124; summarize Count = count() by name<br>&#124; where Count > 3</code>|
