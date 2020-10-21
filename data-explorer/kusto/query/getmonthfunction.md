---
title: 'getmonth ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 getmonth ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 1b3f87c7be8f4b2e12fc0136c163c4df0766eed8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252981"
---
# <a name="getmonth"></a>getmonth()

從日期時間取得月份 (1-12)。

另一個別名： monthoyear ( # A1

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
