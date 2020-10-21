---
title: 'todatetime ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中的 todatetime ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5b9248ddc76a4893cf1edd353c0fbd036c9a1e7d
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250538"
---
# <a name="todatetime"></a>todatetime()

將輸入轉換為 [datetime](./scalar-data-types/datetime.md) 純量。

```kusto
todatetime("2015-12-24") == datetime(2015-12-24)
```

## <a name="syntax"></a>語法

`todatetime(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成 [datetime](./scalar-data-types/datetime.md)的運算式。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是 [日期時間](./scalar-data-types/datetime.md) 值。
否則，結果會是 null。
 
> [!NOTE]
> 最好盡可能使用 [datetime ( # B1 ](./scalar-data-types/datetime.md) 。
