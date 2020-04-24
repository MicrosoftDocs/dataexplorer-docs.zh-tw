---
title: 叉運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的分叉運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 13cd837b3874a55ec758991f5609e089daba7c75
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515193"
---
# <a name="fork-operator"></a>fork 運算子

並行運行多個使用者運算符。

**語法**

*T* `|` T `=` `(` `)` `fork` =`=`名稱 = 子查詢 = 名稱 = 子查詢 ... `(` *name* `)` *name* *subquery* *subquery*

**引數**

* *子查詢*是查詢運算子的下游管道
* *名稱*是子查詢結果表的暫存名稱

**傳回**

多個結果表,每個子查詢一個。

**支援的運算子**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**注意事項**

* [`materialize`](materializefunction.md)功能可用作使用[`join`](joinoperator.md)[`union`](unionoperator.md)或叉腿的替換。
輸入流將按具體緩存,然後緩存的運算式可用於聯接/聯合支腿。

* `name`參數或使用[`as`](asoperator.md)運算符給出的名稱將用作 工具[`Kusto.Explorer`](../tools/kusto-explorer.md)中 命名結果選項卡的名稱。

* 避免與`fork`單個子查詢一起使用。

* 首選使用[帶有](batches.md)[`materialize`](materializefunction.md)表格表達式語句的批`fork`處理而不是 運算符。

**範例**

```kusto
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 )
    ( project Timestamp, EventText | top 1000 by Timestamp desc)
    ( summarize min(Timestamp), max(Timestamp) by ActivityID )
 
// In the following examples the result tables will be named: Errors, EventsTexts and TimeRangePerActivityID
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 | as Errors )
    ( project Timestamp, EventText | top 1000 by Timestamp desc | as EventsTexts )
    ( summarize min(Timestamp), max(Timestamp) by ActivityID | as TimeRangePerActivityID )
    
 KustoLogs
| where Timestamp > ago(1h)
| fork
    Errors = ( where Level == "Error" | project EventText | limit 100 )
    EventsTexts = ( project Timestamp, EventText | top 1000 by Timestamp desc )
    TimeRangePerActivityID = ( summarize min(Timestamp), max(Timestamp) by ActivityID )
```