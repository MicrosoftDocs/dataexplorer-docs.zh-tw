---
title: 查詢-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8be14a48f5b24d344454d9aa5012f48e7d7e2b8e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250967"
---
# <a name="query-operators"></a>查詢運算子

針對 Kusto 引擎叢集的內嵌資料，查詢是唯讀作業。 查詢一律會在叢集中特定資料庫的內容中執行。 它們也可以參考另一個資料庫中的資料，甚至是另一個叢集中的資料。

當資料的臨機操作查詢是最優先的 Kusto 案例時，Kusto 查詢語言語法最適合用於非專家使用者撰寫及執行其資料的查詢，並且能夠明確地瞭解每個查詢在邏輯上) 的 (。

語言語法是資料流程的語言語法，其中「資料」表示「表格式資料」 (一或多個資料列/資料行中的資料，矩形圖形) 。 查詢至少是由來源資料參考所組成 (Kusto 資料表) 的參考，以及一或多個依順序套用的 **查詢運算子** ，使用管道字元 () 分隔運算子，以視覺化方式表示 `|` 。

例如：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```

每個以縱線字元 `|` 做為前置詞的篩選條件即為含有一些參數的「運算子」** 執行個體。 前面管線所得到的結果會以資料表的形式做為運算子的輸入。 大部分情況下，任何參數皆是輸入資料行的 [純量運算式](./scalar-data-types/index.md) 。
但在某些情況下，參數是輸入資料行的名稱或是第二個資料表。 查詢的結果永遠是資料表，即使它只有一個資料行和一個資料列。

`T` 在查詢中用來表示之前的管線或來源資料表。
