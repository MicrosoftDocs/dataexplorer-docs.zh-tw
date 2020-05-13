---
title: hash_sha256 （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 hash_sha256 （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32fa2f3ffefdbf1f14ed87e8e89444de322408c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372325"
---
# <a name="hash_sha256"></a>hash_sha256()

傳回輸入值的 sha256 雜湊值。

**語法**

`hash_sha256(`*來源*`)`

**引數**

* *來源*：要雜湊的值。

**傳回**

指定純量的 sha256 雜湊值，編碼為十六進位字串（字元字串，每兩個都代表0到255之間的一個十六進位數位）。

> [!WARNING]
> 此函式（SHA256）所使用的演算法保證不會在未來進行修改，但對計算而言非常複雜。 在單一查詢期間，需要「輕量」雜湊函式的使用者，建議改用函數[雜湊（）](./hashfunction.md) 。

**範例**

```kusto
hash_sha256("World")                   // 78ae647dc5544d227130a0682a51e30bc7777fbb6d8a8f17007463a3ecd1d524
hash_sha256(datetime("2015-01-01"))    // e7ef5635e188f5a36fafd3557d382bbd00f699bd22c671c3dea6d071eb59fbf8
```

下列範例會使用 hash_sha256 函數，對資料的 StartTime 資料行執行查詢。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where hash_sha256(StartTime) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```
