---
title: tobool() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 tobool()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3e1867c99ae241b3f7e09ab8ee873d5ae5d374e0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506353"
---
# <a name="tobool"></a>tobool()

將輸入轉換為布爾(簽名的8位)表示形式。

```kusto
tobool("true") == true
tobool("false") == false
tobool(1) == true
tobool(123) == true
```

**語法**

`tobool(`*Expr* `)` 
 * * Expr (別名) `toboolean(` `)`

**引數**

* *Expr*: 將轉換為布林的運算式。 

**傳回**

如果轉換成功,結果將是一個布爾。
如果轉換不成功,結果會為`null`。
 