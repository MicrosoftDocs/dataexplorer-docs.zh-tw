---
title: dayofmonth （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 dayofmonth （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3341f416642b06d899c2a3d1f6675f4d3254291f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348491"
---
# <a name="dayofmonth"></a>dayofmonth()

傳回代表給定月份之日數位的整數數位

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

## <a name="syntax"></a>語法

`dayofmonth(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`day number`指定月份的。