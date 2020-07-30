---
title: todecimal （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 todecimal （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f699508436c9e2533661a440be2ac8f5f8d94688
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350769"
---
# <a name="todecimal"></a>todecimal()

將輸入轉換成十進位數表示。

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>語法

`todecimal(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成十進位的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，則結果會是十進位數。
如果轉換不成功，則結果會是 `null` 。
 
*注意*：可能的話，建議您盡可能使用[real （）](./scalar-data-types/real.md) 。