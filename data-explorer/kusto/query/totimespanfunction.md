---
title: totimespan （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 totimespan （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b785f346dd95a9c6a8cb9d6148e889c42ac4b02c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129034"
---
# <a name="totimespan"></a>totimespan()

將輸入轉換為[timespan](./scalar-data-types/timespan.md)純量。

```kusto
totimespan("0.00:01:00") == time(1min)
```

**語法**

`totimespan(Expr)`

**引數**

* *`Expr`*：將轉換成[timespan](./scalar-data-types/timespan.md)的運算式。

**傳回**

如果轉換成功，則結果會是[timespan](./scalar-data-types/timespan.md)值。
否則，結果會是 null。
