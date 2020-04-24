---
title: 具體() - Azure 資料資源管理員 |微軟文件
description: 本文在 Azure 數據資源管理器中介紹具體化。"
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: dfaed8cf972a517c86717999b3e5423c0ddd1fb0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512609"
---
# <a name="materialize"></a>materialize()

允許在查詢執行期間緩存子查詢結果,其他子查詢可以引用部分結果。

 
**語法**

`materialize(`*表達*`)`

**引數**

* *運算式*:在查詢執行期間要計算和快取的表格運算式。

**技巧**

* 當您有 join/union，而其運算元有可執行一次的共同子查詢時，請使用 materialize (請參閱下列範例)。

* 在需要聯結/聯集分岔流程的情況下也很有用。

* Materialize 只允許用在 let 陳述式中來命名快取的結果。

* 具體化具有 5 **GB**的緩存大小限制。 
  此限制是每個群集節點,對於同時運行的所有查詢都是相互的。
  如果查詢使用`materialize()`並且緩存無法保存任何其他數據,則查詢將失敗並出現錯誤。

**範例**

假設我們想要生成一組隨機的值,並且我們有興趣找到我們擁有的多少不同的值、所有這些值的總和和前 3 個值。

這可以使用[批處理](batches.md)進行,並實現:

 ```kusto
let randomSet = materialize(range x from 1 to 30000000 step 1
| project value = rand(10000000));
randomSet
| summarize dcount(value);
randomSet
| top 3 by value;
randomSet
| summarize sum(value)

```