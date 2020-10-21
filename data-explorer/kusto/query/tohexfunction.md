---
title: 'tohex ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文說明 Azure 資料總管中的 tohex ( # A1。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7784424d0053761d4cfdb373dea0ed57f8bca83e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250358"
---
# <a name="tohex"></a>tohex()

將輸入轉換為十六進位字串。

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

## <a name="syntax"></a>語法

`tohex(`*Expr* `, [` ， ` *MinLength*]`) '

## <a name="arguments"></a>引數

* *Expr*：將轉換為十六進位字串的 int 或 long 值。  不支援其他類型。
* *MinLength*：代表要包含在輸出中的前置字元數的數值。  支援介於1和16之間的值，大於16的值會被截斷為16。  如果字串的長度超過 minLength 而不含前置字元，則會有效地忽略 minLength。  負數只能以其基礎資料大小的最小值來表示，因此針對 int (32 位) minLength 最少為8個，長 (64 位) 最少16個。

## <a name="returns"></a>傳回

如果轉換成功，結果將會是字串值。
如果轉換不成功，結果將會是 null。