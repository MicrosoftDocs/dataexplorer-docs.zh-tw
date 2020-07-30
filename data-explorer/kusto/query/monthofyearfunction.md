---
title: monthofyear （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 monthofyear （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: af015146bb2f07d83d4333312a96d5b80c67190a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346723"
---
# <a name="monthofyear"></a>monthofyear()

傳回整數，代表指定年份的月份數。

另一個別名： getmonth （）

```kusto
monthofyear(datetime("2015-12-14"))
```

## <a name="syntax"></a>語法

`monthofyear(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`month number`指定年份。