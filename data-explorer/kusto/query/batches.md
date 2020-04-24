---
title: 批次處理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的批處理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd695a1e7ffd9980de2750d38ad9538eeb877538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518015"
---
# <a name="batches"></a>批次

查詢可以包含多個表格表達式語句,只要它們由分號`;`( ) 字元分隔。 然後,查詢返回多個表格結果,如表格表達式語句生成的結果,並根據查詢文本中語句的順序進行排序。

例如,以下查詢生成兩個表格結果。 然後,使用者代理工具可以使用與每個(`Count of events in Florida``Count of events in Guam`和 ) 關聯的相應名稱顯示這些結果。

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

批次處理對於存在由多個子查詢(如儀表板)共用的常見計算的方案特別有用。 如果常見計算很複雜,建議使用[具體項() 函數](./materializefunction.md)建構查詢,以便只執行一次查詢:

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

注意：
* 更喜歡批次處理,[`materialize`](materializefunction.md)而不是使用[分叉運算子](forkoperator.md)。