---
title: 架構設計的最佳實作 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中架構設計的最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: f9e2d4a2ad50b140d041179736b48a41a424f5c6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522163"
---
# <a name="best-practices-for-schema-design"></a>架構設計的最佳實作

有幾個"做和不做",你可以遵循,使你的管理命令性能更好,對服務有較輕的影響。

## <a name="do"></a>要做的事

1. 如果需要創建多個表,請使用該[`.create tables`](create-tables-command.md)命令,而不是發出許多`.create table`命令。
2. 如果需要重命名多個表,請對[`.rename tables`](rename-table-command.md)執行此項,而不是為每個表發出單獨的調用。
3. 使用範圍最小的`.show`命令,而不是在管道`|`() 之後應用篩選器。 例如：
    - 使用 `.show table T extents` 來取代 `.show cluster extents | where TableName == 'T'`
    - 使用 `.show database DB schema` 取代 `.show schema | where DatabaseName == 'DB'`。
4. 僅當`.show table T`需要在單個表上獲取實際統計資訊時才使用。 如果只需要檢查表的存在,或者只需獲取表的架構,請使用`.show table T schema as json`。
5. 為包含日期時間值的表定義架構時,請確保使用`datetime`類型鍵入這些列。
    - Kusto 是經過高度優化的`datetime`, 適用於對列進行篩選。 不要在查詢`datetime`時`string`將 或數位`long`(例如 )列轉換為以進行篩選,如果可以在引入時間之前或期間進行。

## <a name="dont"></a>不要做的事

1. 不要過於頻繁地`.show`執行命令(`.show schema`例如`.show databases``.show tables`, 。 如果可能 , 緩存他們傳回的資訊。
2. 不要在大型架構(`.show schema`例如具有 100 多個資料庫)的群集上運行命令。 而是使用[`.show databases schema`](../management/show-schema-database.md)。
3. 不要過於頻繁地運行[命令-然後查詢](index.md#combining-queries-and-control-commands)操作。
    - *命令-然後查詢*:意味著管道控制命令的結果集,並在其上應用篩選器/聚合。
        - 例如： `.show ... | where ... | summarize ...`
    - 運行類似: `.show cluster extents | count` (強調`| count`) Kusto 首先準備一個數據表,其中包含群集中所有擴展盤的所有詳細資訊,然後將該記憶體中僅表發送到 Kusto 引擎以執行計數。 這意味著 Kusto 實際上在未優化的路徑中非常努力地工作,可以給你一個微不足道的答案。
4. 過度使用範圍標記作為數據引入的一部分。 尤其是在使用`drop-by:`標記時,這限制了系統在後台執行面向性能的修飾過程的能力。
    - [在此處](../management/extents-overview.md#extent-tagging)查看性能說明。
    