---
title: '計數 ( # A1 (彙總函式) -Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中 ( # A1 (彙總函式) 的計數。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513092"
---
# <a name="count-aggregation-function"></a>count ( # A1 (彙總函式) 

會傳回每個摘要群組的記錄計數 (或總計，如果在沒有群組) 的情況下完成摘要。

* 只能用在[摘要內匯總](summarizeoperator.md)的內容中
* 您可以使用 [countif](countif-aggfunction.md) 彙總函式來計算部分述詞傳回的記錄 `true` 。

## <a name="syntax"></a>語法

總結 `count()`

## <a name="returns"></a>傳回

會傳回每個摘要群組的記錄計數 (或總計，如果在沒有群組) 的情況下完成摘要。

## <a name="example"></a>範例

以字母開頭的狀態計算事件 `W` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|State|計數|
|---|---|
|西維吉尼亞|757|
|懷俄明州|396|
|華盛頓|261|
|威斯康辛州|1850|
