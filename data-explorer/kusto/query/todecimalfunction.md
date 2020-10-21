---
title: 'todecimal ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 todecimal ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4b9f020a1d37b7279c0712951ed0c2e39e9e428
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250509"
---
# <a name="todecimal"></a>todecimal()

將輸入轉換為十進位數表示。

```kusto
todecimal("123.45678") == decimal(123.45678)
```

## <a name="syntax"></a>語法

`todecimal(`*Expr*`)`

## <a name="arguments"></a>引數

* *Expr*：將轉換成 decimal 的運算式。 

## <a name="returns"></a>傳回

如果轉換成功，結果將會是十進位數。
如果轉換不成功，結果將會是 `null` 。
 
*注意*：如果可能的話，最好使用 [Real ( # B1 ](./scalar-data-types/real.md) 。