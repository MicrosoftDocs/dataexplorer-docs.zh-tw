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
ms.openlocfilehash: 1edc5e3ef8c3c2dea65d332e6ceace653cc5c812
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340134"
---
# <a name="totimespan"></a>totimespan()

將輸入轉換為[timespan](./scalar-data-types/timespan.md)純量。

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>語法

`totimespan(Expr)`

## <a name="arguments"></a>引數

* *`Expr`*：將轉換成[timespan](./scalar-data-types/timespan.md)的運算式。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是[timespan](./scalar-data-types/timespan.md)值。
否則，結果會是 null。
