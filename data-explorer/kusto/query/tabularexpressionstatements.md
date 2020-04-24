---
title: 表格運算 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的表位運算式語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1209f96ed99de79d3fa6ac00e3a115a00a6248e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506608"
---
# <a name="tabular-expression-statements"></a>表格式運算式陳述式

表格表達式語句是人們在談論查詢時通常要考慮的。 此語句通常出現在語句清單中的最後一個,並且其輸入和輸出都由表或表格數據集組成。

Kusto 對表格表達式語句使用數據流模型。 表格表示式語句的典型結構是*表格資料來源*(如 Kusto 表)、*表格資料運算符*(如篩選器和投影)和潛在*呈現運算符*的組成。 合成由管道字元 ()`|`表示,為語句提供非常常規的形式,直觀地表示從左到右的表格數據流。
每個運算子接受「從管道」的表格資料集,以及來自運算符正文的其他輸入(包括其他表格資料集),然後將表格資料集發出給下一個運算符號:   

*來源1* `|` *運算子1* `|` *運算子2* `|` *成像指令*

在下面的範例中,源是`Logs`(對目前資料庫中的表的引用),第一個運算符是`where`(根據某些每記錄謂詞從輸入中篩選出記錄),第二個運算元是`count`(計算其輸入資料集中的記錄數):

```kusto
Logs | where Timestamp > ago(1d) | count
```

在下面的更複雜的範例中,`join`運算子用於合併兩個輸入資料集的記錄:一個是`Logs`表上的篩選器,另一個是`Events`表上的篩選器。

```kusto
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
```

## <a name="tabular-data-sources"></a>表格資料來源

**表格資料來源**生成記錄集,由**表格數據運算子**進一步處理。 Kusto 支援以下多個來源:

* 表引用(引用上下文資料庫中的 Kusto 表或其他群集/資料庫中)。
* 表格[範圍運算子](rangeoperator.md)。
* [列印運算子](printoperator.md)。
* 返回表的函數的調用。
* [表文字](datatableoperator.md)("資料表")。