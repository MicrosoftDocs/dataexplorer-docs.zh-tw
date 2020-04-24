---
title: 至長() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 tolong()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd354a39c048631ab98390c74cb7cd78ee5376b2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506081"
---
# <a name="tolong"></a>tolong()

將輸入轉換為長(已簽名的 64 位)數位表示形式。

```kusto
tolong("123") == 123
```

**語法**

`tolong(`*Expr*`)`

**引數**

* *Expr*: 將轉換為長的運算式。 

**傳回**

如果轉換成功,結果將是一個長數。
如果轉換不成功,結果會為`null`。
 
*注意*:如果可能,最好使用[long()。](./scalar-data-types/long.md)