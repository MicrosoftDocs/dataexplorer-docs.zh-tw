---
title: 哈希() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的哈希()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f8142c42dcb0874dfbd84515e56dc8765bcba3d7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514139"
---
# <a name="hash"></a>hash()

返回輸入值的哈希值。

**語法**

`hash(`*來源*`,`[ *mod*]`)`

**引數**

* *源*:要哈希的值。
* *mod*: 要套用於哈希結果的選擇模組值,以便輸出值介於`0`*mod* - 1 之間

**傳回**

給定標量的哈希值,蒙杜洛給定的 mod 值(如果指定)。

> [!WARNING]
> 用於計算哈希的演演演算法是xxhash。
> 此演算法將來可能會更改,並且唯一的保證是在單個查詢中此方法的所有調用使用相同的演演演算法。
> 因此,建議使用者不要將的結果`hash()`存儲在表中。 如果需要持久化哈希值,請考慮改用[hash_sha256(](./sha256hashfunction.md)但請注意,計算`hash()`它比 要複雜得多)。

**範例**

```kusto
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```

下面的範例使用哈希函數對 10% 的資料執行查詢,當假定值均勻分佈時,使用哈希函數對資料進行取樣非常有用(在此範例中為 StartTime 值)

```kusto
StormEvents 
| where hash(StartTime, 10) == 0
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State 
| top 5 by StormCount desc
```