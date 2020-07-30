---
title: 分叉運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的分支運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b234a95b4a541099f3fc050501ca6b0fd9f67ccf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347998"
---
# <a name="fork-operator"></a>fork 運算子

以平行方式執行多個取用者運算子。

## <a name="syntax"></a>語法

*T* `|` `fork` [*name* `=` ] `(` *子查詢* `)` [*name* `=` ] `(` *子查詢* `)` .。。

## <a name="arguments"></a>引數

* *子*查詢是查詢運算子的下游管線
* *name*是子查詢結果資料表的暫時名稱

## <a name="returns"></a>傳回

多個結果資料表，每個子查詢各一個。

**支援的運算子**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**備註**

* [`materialize`](materializefunction.md)函式可用來取代 [`join`](joinoperator.md) [`union`](unionoperator.md) 在分叉支線上使用或。
輸入資料流程會藉由具體化進行快取，然後快取的運算式可以用於聯結/聯集的腿。

* `name`引數或 using 運算子所指定的名稱， [`as`](asoperator.md) 將用來做為工具中的 [結果] 索引標籤名稱 [`Kusto.Explorer`](../tools/kusto-explorer.md) 。

* 避免 `fork` 在單一子查詢中使用。

* 偏好搭配使用[batch](batches.md)與 [`materialize`](materializefunction.md) 表格式運算式語句 over `fork` 運算子。

## <a name="examples"></a>範例

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