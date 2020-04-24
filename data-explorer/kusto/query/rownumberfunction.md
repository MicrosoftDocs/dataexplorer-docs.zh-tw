---
title: row_number() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的row_number()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8cb01ed098d24632154215ddf06dc2ab1d72695
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510161"
---
# <a name="row_number"></a>row_number()

在[序列化行集中](./windowsfunctions.md#serialized-row-set)返回當前行的索引。
行索引預設從第`1`一行開始,並且每行增加`1`。
或者,行索引可以從與`1`的值不同的開始。
此外,還可以根據某些提供的謂詞重置行索引。

**語法**

`row_number``(` 【*開始索引*】`,` *重新啟動**`)`

* *StartIndex*是`long`一種類型的常量運算式,指示要開始(或重新啟動到)的行索引的值。 預設值是 `1`。
* *重新啟動*是一種可選的`bool`類型 參數,指示何時將編號重新啟動到 *「啟動索引」* 值。 如果未提供,則使用`false`的 預設值。

**傳回**

函數將當前行的行索引作為類型`long`的值返回。

**範例**

下面的範例傳回一欄的表,`a`第一列 ()`10`的數字從`1`下到,`rn`第二`1`欄`10`( ), 數字從最高到 :

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

下面的示例與上述示例類似,只有第二列`rn`(`7`) 從 開始。

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

最後一個示例演示如何對數據進行分區,併為每個分區對行進行編號。 在這裏,我們`Airport`按 :

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

執行此查詢會產生以下結果:

機場  | 航空公司  | 離開  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | Lh       | 1           | 2
SEA      | LY       | 0           | 3
TLV      | LY       | 100         | 1
TLV      | Lh       | 1           | 2