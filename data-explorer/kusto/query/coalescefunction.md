---
title: '聯合 ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的聯合 ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3eb5e533c2b4430f54909507e521912711c35811
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252644"
---
# <a name="coalesce"></a>coalesce()

評估運算式的清單，並傳回字串) 運算式的第一個非 null (或非空白的。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>語法

`coalesce(`*expr_1* `, `*expr_2* `,`... ) 

## <a name="arguments"></a>引數

* *expr_i*：要評估的純量運算式。
- 所有引數都必須是相同的類型。
- 最多可支援64個引數。


## <a name="returns"></a>傳回

第一個 *expr_i* 的值，其值不是 null (或非空白的字串運算式) 。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
