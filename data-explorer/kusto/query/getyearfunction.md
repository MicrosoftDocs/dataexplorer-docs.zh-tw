---
title: getyear （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 getyear （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 032cc319661218e77d5b23e6c649de7d5856d6c9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347641"
---
# <a name="getyear"></a>getyear()

傳回引數的年部分 `datetime` 。

## <a name="example"></a>範例

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```