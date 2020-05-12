---
title: 聯合（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的聯合（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea57efe36fb86189d798e5f18fa3fe9470bfd634
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227531"
---
# <a name="coalesce"></a>coalesce()

評估運算式的清單，並傳回第一個非 null （或非空白的字串）運算式。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**語法**

`coalesce(`*expr_1* `, `*expr_2* `,`...)

**引數**

* *expr_i*：要評估的純量運算式。
- 所有引數都必須是相同的類型。
- 最多支援64個引數。


**傳回**

第一個*expr_i*的值，其值為非 null （若為字串運算式，則為不是空的）。

**範例**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
