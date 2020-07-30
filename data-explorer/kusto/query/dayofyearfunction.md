---
title: dayofyear （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 dayofyear （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 925b65846c6ba32163bd325fd2ee3321bc7fc802
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348457"
---
# <a name="dayofyear"></a>dayofyear()

傳回整數，代表指定年份的日數。

```kusto
dayofyear(datetime(2015-12-14))
```

## <a name="syntax"></a>語法

`dayofweek(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`day number`指定年份。