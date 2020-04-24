---
title: 序列化運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的序列化運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5402d1e1fcceb42f02643bf24918ed07beddaed7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509107"
---
# <a name="serialize-operator"></a>serialize 運算子

標記輸入行集的順序對視窗函數的使用是安全的。

運算碼具有聲明性含義,它將輸入行集標記為序列化(有序),以便[視窗函數](./windowsfunctions.md)可以應用於它。

```kusto
T | serialize rn=row_number()
```

**語法**

`serialize`[*名稱1* `=` *Expr1* ]`,` *名稱2* `=` *Expr2*[...]

* *名稱*/*Expr*對與[擴展運算符](./extendoperator.md)中的對類似。

**範例**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| serialize

Traces
| where ActivityId == "479671d99b7b"
| serialize rn = row_number()
```

以下運算子輸出行集標記為序列化:

[範圍](./rangeoperator.md),[排序](./sortoperator.md),[順序](./topoperator.md),[頂端,](./tophittersoperator.md)[頂端, 取得模式](./getschemaoperator.md).[order](./orderoperator.md)

以下運算子輸出行集標記為非序列化:

[樣本](./sampleoperator.md),[樣本不同](./sampledistinctoperator.md),[不同](./distinctoperator.md),[聯結](./joinoperator.md),[頂巢巢 ,](./topnestedoperator.md)[計數](./countoperator.md),[總結](./summarizeoperator.md),[面,](./facetoperator.md) [mv-展開](./mvexpandoperator.md),[評估](./evaluateoperator.md),[減少](./reduceoperator.md),[使系列](./make-seriesoperator.md)

所有其他運算元都保留序列化屬性(如果輸入行集已序列化,則輸出行集也是如此)。