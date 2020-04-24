---
title: hash_sha256() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的hash_sha256()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2147f4e9f2bd3d7df8f75ac704a4e4808e69bb3c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507594"
---
# <a name="hash_sha256"></a>hash_sha256()

返回輸入值的 sha256 哈希值。

**語法**

`hash_sha256(`*源*`)`

**引數**

* *源*:要哈希的值。

**傳回**

給定標量的 sha256 哈希值,編碼為十六進位元字串(字串,每個字元表示介於 0 和 255 之間的單個十六進位數位)。

> [!WARNING]
> 此函數 (SHA256) 使用的演算法保證將來不會修改,但計算非常複雜。 建議在單個查詢期間需要「輕量級」 哈希函數的使用者改用函數[哈希()。](./hashfunction.md)

**範例**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

下面的範例使用hash_sha256函數在資料的 StartTime 列上執行查詢

```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```