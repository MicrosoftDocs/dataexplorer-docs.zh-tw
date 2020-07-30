---
title: count （）（彙總函式）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 count （）（彙總函式）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.openlocfilehash: a5f5f554373331d66a08e7166249e8e24c4fbd7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348780"
---
# <a name="count-aggregation-function"></a>count （）（彙總函式）

傳回每個摘要群組的記錄計數（如果未進行分組，則會匯總）。

* 只能在[匯總](summarizeoperator.md)的內容中使用
* 使用[countif](countif-aggfunction.md)彙總函式，只計算某些述詞傳回的記錄 `true` 。

## <a name="syntax"></a>語法

作`count()`

## <a name="returns"></a>傳回

傳回每個摘要群組的記錄計數（如果未進行分組，則會匯總）。

## <a name="example"></a>範例

以字母開頭的狀態計算事件計數 `W` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|狀態|計數|
|---|---|
|西維吉尼亞|757|
|懷俄明州|396|
|位於|261|
|威斯康辛|1850|
