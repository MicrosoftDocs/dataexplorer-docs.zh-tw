---
title: 'getyear ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 getyear ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a820d05b453be7cc991f8068644d33ff990115fc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251155"
---
# <a name="getyear"></a>getyear()

傳回引數的年份部分 `datetime` 。

## <a name="example"></a>範例

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```