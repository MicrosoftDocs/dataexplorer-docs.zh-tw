---
title: tohex （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 tohex （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6cc9beb5f5229505cf5ac40f95de6bafeb979f6f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350701"
---
# <a name="tohex"></a>tohex()

將輸入轉換成十六進位字串。

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

## <a name="syntax"></a>語法

`tohex(`*Expr* `, [` ， ` *MinLength*]` ） '

## <a name="arguments"></a>引數

* *Expr*：將轉換成十六進位字串的 int 或 long 值。  不支援其他類型。
* *MinLength*：代表要包含在輸出中的前置字元數的數值。  支援介於1到16之間的值，大於16的值將會被截斷為16。  如果字串長度超過 minLength 而沒有開頭字元，則會有效忽略 minLength。  負數最少只能以其基礎資料大小表示，因此，對於 int （32位），minLength 最小值為8，長（64位）最小值為16。

## <a name="returns"></a>傳回

如果轉換成功，則結果會是字串值。
如果轉換不成功，則結果會是 null。