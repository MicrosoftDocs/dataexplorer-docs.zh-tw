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
ms.openlocfilehash: 410a0c84a1bafdfa1900ef8e21bc0a91327b64c3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348865"
---
# <a name="coalesce"></a>coalesce()

評估運算式的清單，並傳回第一個非 null （或非空白的字串）運算式。

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

## <a name="syntax"></a>語法

`coalesce(`*expr_1* `, `*expr_2* `,`...)

## <a name="arguments"></a>引數

* *expr_i*：要評估的純量運算式。
- 所有引數都必須是相同的類型。
- 最多支援64個引數。


## <a name="returns"></a>傳回

第一個*expr_i*的值，其值為非 null （若為字串運算式，則為不是空的）。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
