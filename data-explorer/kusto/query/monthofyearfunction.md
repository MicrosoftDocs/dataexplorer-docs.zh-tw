---
title: 'monthofyear ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 monthofyear ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd40612236b9c9b249c9c070bc784c518be0faae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251000"
---
# <a name="monthofyear"></a>monthofyear()

傳回整數值，代表指定年份的月數。

另一個別名： getmonth ( # A1

```kusto
monthofyear(datetime("2015-12-14"))
```

## <a name="syntax"></a>語法

`monthofyear(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`month number` 在指定年份中。