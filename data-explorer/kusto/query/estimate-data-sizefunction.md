---
title: 'estimate_data_size ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 estimate_data_size。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09d722b07b3ff49a294d4d8ce19a89563fc8f66e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253021"
---
# <a name="estimate_data_size"></a>estimate_data_size()

傳回表格式運算式所選資料行的預估資料大小（以位元組為單位）。

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

## <a name="syntax"></a>語法

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, `*col2* `, `...`)`

## <a name="arguments"></a>引數

* *col1*、 *col2*：選取來源表格式運算式中用於資料大小估計的資料行參考。 若要包含所有資料行，請使用 `*` (星號) 語法。

## <a name="returns"></a>傳回

* 記錄大小的預估資料大小（以位元組為單位）。 估計是根據資料類型和值長度。

## <a name="examples"></a>範例

使用下列內容計算資料大小總計 `estimated_data_size()` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|總計|
|---|
|180|
