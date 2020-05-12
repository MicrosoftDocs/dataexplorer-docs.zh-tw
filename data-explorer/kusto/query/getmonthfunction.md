---
title: getmonth （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 getmonth （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: e04abb6eed95e5129d6878486e3781173cbf0557
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226783"
---
# <a name="getmonth"></a>getmonth()

從日期時間取得月份 (1-12)。

另一個別名： monthoyear （）

**範例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
