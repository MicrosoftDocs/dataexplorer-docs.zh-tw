---
title: hourofday （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 hourofday （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5a001a2f9faa1eb7ea3636ee6fbfde3cb0489158
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347505"
---
# <a name="hourofday"></a>hourofday()

傳回代表指定日期之小時數的整數

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

## <a name="syntax"></a>語法

`hourofday(`*a_date*`)`

## <a name="arguments"></a>引數

* `a_date`：`datetime`。

## <a name="returns"></a>傳回

`hour number`日（0-23）。