---
title: 查詢 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2ac338600320d3f83a92e22e405f630ee308df2f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510773"
---
# <a name="queries"></a>查詢

查詢是針對 Kusto 引擎群集的引入數據的唯讀操作。 查詢始終在群集中特定資料庫的上下文中運行(儘管它們也可能引用另一個資料庫中甚至另一個群集中的數據)。

由於數據臨時查詢是 Kusto 的重中之重,因此 Kusto 查詢語言語法針對非專家使用者對其數據進行創作和運行查詢進行了優化,並能夠明確瞭解每個查詢的作用(邏輯)。

語言語法是資料流的語法,「數據」實際上表示「表格數據」(一個或多個行/列矩形形狀中的數據)。 至少,查詢由源數據引用(對 Kusto 表的引用)和按順序應用的一個或多個**查詢運算元**組成,透過使用管道字`|`元 ( ) 來分隔運算符直觀地指示。

例如：

```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```
    
每個以縱線字元 `|` 做為前置詞的篩選條件即為含有一些參數的「運算子」** 執行個體。 前面管線所得到的結果會以資料表的形式做為運算子的輸入。 大部分情況下，任何參數皆是輸入資料行的 [純量運算式](./scalar-data-types/index.md) 。
但在某些情況下，參數是輸入資料行的名稱或是第二個資料表。 查詢的結果永遠是資料表，即使它只有一個資料行和一個資料列。

## <a name="reference-query-operators"></a>參考:查詢運算子

> `T` 來表示前面的管線或來源資料表。