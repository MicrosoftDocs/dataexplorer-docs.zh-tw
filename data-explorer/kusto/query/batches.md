---
title: 批次-Azure 資料總管
description: 本文說明 Azure 資料總管中的批次。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: efd4b4c5387016bd66027bcf5e0722f8039f0837
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252626"
---
# <a name="batches"></a>批次

查詢可以包含多個表格式運算式語句，只要它們是以分號分隔 (`;`) 字元。 然後查詢會傳回多個表格式結果。 結果是由表格式運算式語句所產生，並根據查詢文字中的語句順序進行排序。

例如，下列查詢會產生兩個表格式結果。 然後，使用者代理程式工具可以使用與每個 (相關聯的適當名稱來顯示這些結果 `Count of events in Florida` `Count of events in Guam` ，並分別) 。

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Batch 適用于由多個子查詢（例如儀表板）共用一般計算的案例。 如果一般計算很複雜，請使用 [具體化 ( # A1 函數](./materializefunction.md) 並建立查詢，使其只會執行一次：

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

注意：
* 偏好批次處理和 [`materialize`](materializefunction.md) 使用 [分叉運算子](forkoperator.md)。
