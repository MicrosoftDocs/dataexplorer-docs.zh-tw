---
title: count() (彙總函式) - Azure 資料總管 | Microsoft Docs
description: 本文描述 Azure 資料總管中的 count() (彙總函式)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513092"
---
# <a name="count-aggregation-function"></a>count() (彙總函式)

傳回每個摘要群組的記錄計數 (如果摘要未進行分組則傳回總數)。

* 僅用於 [summarize](summarizeoperator.md) 內部彙總的內容。
* 使用 [countif](countif-aggfunction.md) 彙總函式，即可只計算某些述詞傳回 `true` 的記錄。

## <a name="syntax"></a>語法

summarize `count()`

## <a name="returns"></a>傳回

傳回每個摘要群組的記錄計數 (如果摘要未進行分組則傳回總數)。

## <a name="example"></a>範例

針對以字母 `W` 開始的州，計算其中的活動：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|州|計數|
|---|---|
|西維吉尼亞|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|
