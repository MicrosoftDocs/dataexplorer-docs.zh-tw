---
title: estimate_data_size （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 estimate_data_size （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f0901bddbfa8854e902ab60197164cf830215948
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224947"
---
# <a name="estimate_data_size"></a>estimate_data_size()

傳回表格式運算式所選資料行的估計資料大小（以位元組為單位）。

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

**語法**

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, `*col2* `, `...`)`

**引數**

* *col1*、 *col2*：選取用於資料大小估計的來源表格式運算式中的資料行參考。 若要包含所有資料行，請使用 `*` （星號）語法。

**傳回**

* 記錄大小的估計資料大小（以位元組為單位）。 估計是以資料類型和值長度為基礎。

**範例**

使用下列各計算資料大小總計 `estimated_data_size()` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|總計|
|---|
|180|
