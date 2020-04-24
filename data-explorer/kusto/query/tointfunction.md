---
title: toint() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 toint()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570e13dc816c8a7e6d5baa488912fd8def5d2883
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506098"
---
# <a name="toint"></a>toint()

將輸入轉換為整數(簽名的 32 位)數位表示形式。

```kusto
toint("123") == 123
```

**語法**

`toint(`*Expr*`)`

**引數**

* *Expr*: 將轉換為整數的運算式。 

**傳回**

如果轉換成功,結果將是一個整數。
如果轉換不成功,結果會為`null`。
 
*注意*:如果可能,最好使用[int()。](./scalar-data-types/int.md)