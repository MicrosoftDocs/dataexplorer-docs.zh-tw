---
title: 批次-Azure 資料總管
description: 本文說明 Azure 資料總管中的批次。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e5a38d53fb9b28fc7da0ddf71132e3a047e8188e
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550328"
---
# <a name="batches"></a>批次

查詢可以包含多個表格式運算式語句，前提是它們是以分號（ `;` ）字元分隔。 然後查詢會傳回多個表格式結果。 結果是由表格式運算式語句所產生，並根據查詢文字中的語句順序排序。

例如，下列查詢會產生兩個表格式結果。 然後，使用者代理程式工具可以使用與每個（ `Count of events in Florida` 和分別）相關聯的適當名稱來顯示這些結果 `Count of events in Guam` 。

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Batch 適用于多個子查詢（例如儀表板）共用一般計算的案例。 如果一般計算很複雜，請使用[具體化（）函數](./materializefunction.md)並建立查詢，使其只會執行一次：

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

附註：
* 偏好批次處理，並 [`materialize`](materializefunction.md) 使用[分叉運算子](forkoperator.md)。
