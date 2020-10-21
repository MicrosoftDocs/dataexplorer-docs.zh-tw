---
title: 'hourofday ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 hourofday ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6d83725e067f12d05382a905aee935f9433ef7a9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244712"
---
# <a name="hourofday"></a>hourofday()

傳回代表指定日期的小時數的整數。

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>語法

`hourofday(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`hour number` 在當日 (0-23) 。