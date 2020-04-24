---
title: estimate_data_size() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的estimate_data_size()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9664a7c9caf0506cdb7274eed5e143f89c82cebb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515720"
---
# <a name="estimate_data_size"></a>estimate_data_size()

返回表格表達式所選列的估計數據大小(以位元組為單位)。

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**語法**

`estimate_data_size(*)`

`estimate_data_size(`*col1*`, `*col2*`, `...`)`

**引數**

* *col1* *col1,col2:* 選擇用於數據大小估計的源表格表達式中的列引用。 要包括所有列,請使用`*`(星號)語法。

**傳回**

* 以記錄大小為位元組的估計數據大小。 估計基於數據類型和值長度。

**範例**

使用 計算總資料`estimated_data_size()`大小 :

```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|總計|
|---|
|180|