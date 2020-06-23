---
title: todatetime （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 todatetime （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f5c108b670534728f34db8975f16d713848dd8f4
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264603"
---
# <a name="todatetime"></a>todatetime()

將輸入轉換為[datetime](./scalar-data-types/datetime.md)純量。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

**語法**

`todatetime(`*Expr*`)`

**引數**

* *Expr*：將轉換成[datetime](./scalar-data-types/datetime.md)的運算式。

**傳回**

如果轉換成功，則結果會是[日期時間](./scalar-data-types/datetime.md)值。
否則，結果會是 null。
 
> [!NOTE]
> 可能的話，偏好使用[datetime （）](./scalar-data-types/datetime.md) 。
