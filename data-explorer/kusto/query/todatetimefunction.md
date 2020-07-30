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
ms.openlocfilehash: 6a991f9f64decbb3985830cf2611361d6c66db31
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350837"
---
# <a name="todatetime"></a>todatetime()

將輸入轉換為[datetime](./scalar-data-types/datetime.md)純量。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>語法

`todatetime(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成[datetime](./scalar-data-types/datetime.md)的運算式。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是[日期時間](./scalar-data-types/datetime.md)值。
否則，結果會是 null。
 
> [!NOTE]
> 可能的話，偏好使用[datetime （）](./scalar-data-types/datetime.md) 。
