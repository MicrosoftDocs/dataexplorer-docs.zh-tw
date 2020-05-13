---
title: 查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fb842bcda70c2986bd754f55184413eec594d412
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373118"
---
# <a name="queries"></a>查詢

查詢是針對 Kusto 引擎叢集的內嵌資料進行唯讀作業。 查詢一律會在叢集中特定資料庫的內容中執行（雖然它們也可以參考另一個資料庫中的資料，甚至是另一個叢集中的資料）。

由於資料的臨機操作查詢是 Kusto 的最高優先順序案例，因此 Kusto 查詢語言語法已針對非專家使用者針對其資料撰寫和執行查詢進行優化，並能夠明確瞭解每個查詢的運作（以邏輯方式）。

語言語法是資料流程的，其中「資料」實際上表示「表格式資料」（一個或多個資料列/資料行中的資料為矩形圖形）。 查詢至少包含來源資料參考（對 Kusto 資料表的參考），以及一或多個依序套用的**查詢運算子**，使用管線字元（ `|` ）來分隔運算子，以視覺化方式表示。

例如：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```
    
每個以縱線字元 `|` 做為前置詞的篩選條件即為含有一些參數的「運算子」** 執行個體。 前面管線所得到的結果會以資料表的形式做為運算子的輸入。 大部分情況下，任何參數皆是輸入資料行的 [純量運算式](./scalar-data-types/index.md) 。
但在某些情況下，參數是輸入資料行的名稱或是第二個資料表。 查詢的結果永遠是資料表，即使它只有一個資料行和一個資料列。

## <a name="reference-query-operators"></a>參考：查詢運算子

> `T` 來表示前面的管線或來源資料表。
