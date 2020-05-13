---
title: 具體化（）-Azure 資料總管
description: 本文說明 Azure 資料總管中的具體化（）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: 0cf510a8a7a6042d99587b51ee62eb30d4b7b7a7
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271293"
---
# <a name="materialize"></a>materialize()

允許在查詢執行期間以其他子查詢可以參考部分結果的方式，來快取子查詢結果。

 
**語法**

`materialize(`*運算式*`)`

**引數**

* *expression*：要在查詢執行期間評估和快取的表格式運算式。

**提示**

* 當其運算元具有可執行一次的相互子查詢時，請使用具體化搭配 join 或 union。 請參閱以下範例。

* 在需要聯結/聯集分岔流程的情況下也很有用。

* 當您提供快取的結果名稱時，具體化只能在 let 語句中使用。


* 具體化的快取大小限制為**5 GB**。 
  這是每個叢集節點的限制，而且會與同時執行的所有查詢相互同步。
  如果查詢使用 `materialize()` ，而且快取無法保存任何其他資料，則查詢將會中止並產生錯誤。

**範例**

我們想要產生一組隨機的值，而且想要知道： 
 * 我們擁有多少相異值 
 * 所有這些值的總和 
 * 前三個值

這種作業可以使用[批次](batches.md)和具體化來完成：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
