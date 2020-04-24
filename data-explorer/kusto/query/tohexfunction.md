---
title: tox() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 tox()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 402a0923d4fe760e97fa6098ad955b6c24a5d5ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506115"
---
# <a name="tohex"></a>tohex()

將輸入轉換為十六進位元字串。

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**語法**

`tohex(`*Expr*`, [`` *MinLength*]`, '

**引數**

* *Expr:* int 或長值,將轉換為十六進位元字串。  不支援其他類型的類型。
* *最小長度*:表示要在輸出中包括的前導字元數的數值。  支援介於 1 和 16 之間的值,大於 16 的值將被截斷為 16。  如果字串長於最小長度而不帶前導符,則有效忽略最小長度。  負數只能由其基礎數據大小以最小表示,因此對於 int (32 位元)最小長度將至少為 8,對於長(64 位),最小為 16。

**傳回**

如果轉換成功,則結果將是字串值。
如果轉換不成功,結果將為空。