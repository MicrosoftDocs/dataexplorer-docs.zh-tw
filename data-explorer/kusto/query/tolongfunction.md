---
title: tolong （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 tolong （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fb209ff3784f6e24f184b576a1e8f94c52834ba4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340950"
---
# <a name="tolong"></a>tolong()

將輸入轉換成 long （帶正負號的64位）數位表示。

```kusto
tolong("123") == 123
```

## <a name="syntax"></a>語法

`tolong(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成 long 的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，則結果會是長數位。
如果轉換不成功，則結果會是 `null` 。
 
*注意*：可能的話，建議使用[long （）](./scalar-data-types/long.md) 。