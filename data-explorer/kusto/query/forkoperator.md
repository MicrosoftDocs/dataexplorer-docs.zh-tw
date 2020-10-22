---
title: 分叉運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的分叉運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8c20cb3311a254f0154efa01c9d155cd6352c6ef
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356549"
---
# <a name="fork-operator"></a>fork 運算子

平行執行多個取用者運算子。

## <a name="syntax"></a>語法

*T* `|` `fork` [*name* `=` ] `(` *子查詢* `)` [*name* `=` ] `(` *子查詢* `)` .。。

## <a name="arguments"></a>引數

* *子* 查詢是查詢運算子的下游管線
* *名稱* 是子查詢結果資料表的暫存名稱

## <a name="returns"></a>傳回

多個結果資料表，每個子查詢一個。

**支援的運算子**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`project-keep`](project-keep-operator.md), [`project-rename`](projectrenameoperator.md), [`project-reorder`](projectreorderoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**備註**

* [`materialize`](materializefunction.md) 函式可用來取代 [`join`](joinoperator.md) [`union`](unionoperator.md) 分叉腿上的使用或。
輸入資料流程將由具體化來快取，然後快取的運算式可用於聯結/聯集連結中。

* `name`引數或 using 運算子所指定的名稱 [`as`](asoperator.md) 將會用來做為工具中 [結果] 索引標籤的名稱 [`Kusto.Explorer`](../tools/kusto-explorer.md) 。

* 避免搭配 `fork` 單一子查詢使用。

* 偏好搭配使用 [batch](batches.md) 與 [`materialize`](materializefunction.md) 表格式運算式語句 over `fork` 運算子。

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