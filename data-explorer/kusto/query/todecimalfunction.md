---
title: 到十進位() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的十進位()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d5ac70dfe71f80c3963292516e1b8516a297875
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506302"
---
# <a name="todecimal"></a>todecimal()

將輸入轉換為十進位數位表示形式。

```kusto
todecimal("123.45678") == decimal(123.45678)
```

**語法**

`todecimal(`*Expr*`)`

**引數**

* *Expr*: 將轉換為十進位的運算式。 

**傳回**

如果轉換成功,結果將為十進位數位。
如果轉換不成功,結果會為`null`。
 
*注意*:盡可能選擇[使用真實()。](./scalar-data-types/real.md)