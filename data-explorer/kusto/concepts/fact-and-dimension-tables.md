---
title: 事實和維度表 - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的事實和維度表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
ms.openlocfilehash: 1a88f8549bf5accce197294d433ece8ccb683281
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523234"
---
# <a name="fact-and-dimension-tables"></a>事實和維度表

在 Kusto 資料庫設計架構時,將表視為大致屬於兩個類別之一非常有用:
* [事實表](https://en.wikipedia.org/wiki/Fact_table)和
* [維度表](https://en.wikipedia.org/wiki/Dimension_(data_warehouse)#Dimension_table)。

**事實表**是記錄不可變的"事實"的表,如服務日誌和度量資訊。 記錄將逐步追加到表中(以流式或大塊方式),並保留在那裡,直到由於成本原因或丟失價值而必須刪除記錄。 否則,記錄永遠不會更新。

**維度表**保存引用數據(如從實體識別碼到其屬性的查找表)和類似快照的數據(其整個內容在單個事務中更改的表)。 通常,此類表不會定期引入新數據。 相反,使用[諸如 .set 或替換](../management/data-ingestion/ingest-from-query.md)[、.move 擴展區](../management/extents-commands.md#move-extents)或[.重新命名表](../management/rename-table-command.md)等操作一次更新整個資料內容。
在某些情況下,維度表可能通過事實數據表上的[更新策略](../management/updatepolicy.md)派生自事實數據表,以及對獲取每個實體的最後一條記錄的表的一些查詢。

請務必認識到,無法將 Kusto 中的表標記為事實表或維度表。 數據如何引入到表中以及如何使用表是最重要的。

> [!NOTE]
> 有時,Kusto 用於在實際上表中保存實體數據,以便實體數據更改緩慢。 例如,有關某些實體實體(例如,辦公設備)的數據很少更改位置。
> 由於 Kusto 中的數據是不可變的,因此通常的做法是讓每個表包含兩列:標識`string`實體的 標識 ( )`datetime`列和最後修改的 ( ) 時間戳列。 然後僅檢索每個實體標識的最後一條記錄。



## <a name="commands-that-differentiate-fact-and-dimension-tables"></a>區分事實與維度表的指令

Kusto 中有些進程區分了事實表和維度表。

* [連續出口](../management/data-export/continuous-data-export.md)




通過依賴[資料庫游標](../management/databasecursor.md)機制,這些進程可以精確處理一次實際上表中的數據。

例如,每次執行連續匯出作業都會匯出自上次更新資料庫游標以來引入的所有記錄。 因此,連續匯出作業必須區分事實表(僅處理新引入的數據)和維度表(用作查找,因此必須考慮整個表)。