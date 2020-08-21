---
title: 。顯示資料表詳細資料-Azure 資料總管 |Microsoft Docs
description: 本文說明：在 Azure 資料總管中顯示資料表詳細資料。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5c6ceecba95911e57bad9204aeb5a48644db52ef
ms.sourcegitcommit: 94a52929380f0070de1ed8c2011bbb62ceb341fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88702488"
---
# <a name="show-table-details"></a>.show 資料表詳細資料
傳回集合，其中包含指定的資料表或資料庫中的所有資料表，以及每個資料表屬性的詳細摘要。

需要 [資料庫檢視器許可權](../management/access-control/role-based-authorization.md)。

```kusto
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**輸出**

| 輸出參數           | 類型     | 描述                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | 資料表的名稱。                                                                          |
| `DatabaseName`             | String   | 資料表所屬的資料庫。                                                         |
| `Folder`                   | String   | 資料表的資料夾。                                                                             |
| `DocString`                | String   | 記錄資料表的字串。                                                                 |
| `TotalExtents`             | Int64    | 資料表中的延伸區總數。                                                       |
| `TotalExtentSize`          | Double   | 資料表中的範圍大小總計 (壓縮大小 + 索引大小)  (以位元組為單位) 。               |
| `TotalOriginalSize`        | Double   | 資料表中資料的原始大小總計（以位元組為單位） (（以位元組為單位）) 。                                        |
| `TotalRowCount`            | Int64    | 資料表中的總數據列數。                                                          |
| `HotExtents`               | Int64    | 資料表中儲存在熱快取中的延伸區總數。                              |
| `HotExtentSize`            | Double   | 儲存在經常性存取快取 (（以位元組為單位) ）的範圍中，範圍的總大小 (壓縮大小 + 索引大小) 。 |
| `HotOriginalSize`          | Double   | 資料表中儲存的原始資料大小總計（以位元組為單位），儲存在熱快取 (位元組) 。               |
| `HotRowCount`              | Int64    | 資料表中儲存在熱快取中的資料列總數。                                 |
| `AuthorizedPrincipals`     | String   | 資料表的授權主體（序列化為 JSON）。                                          |
| `RetentionPolicy`          | String   | 資料表的有效 `*` 保留原則（序列化為 JSON）。                                  |
| `CachingPolicy`            | String   | 資料表的有效快取 `*` 原則（序列化為 JSON）。                                    |
| `ShardingPolicy`           | String   | 資料表的有效 `*` 分區化原則，序列化為 JSON. 66666666666666                     |
| `MergePolicy`              | String   | 資料表的有效 `*` 合併原則（序列化為 JSON）。                                      |
| `StreamingIngestionPolicy` | String   | 資料表的有效 `*` 串流內嵌原則（序列化為 JSON）。                        |
| `MinExtentsCreationTime`   | Datetime | 資料表中範圍的最小建立時間 (或 null （如果沒有任何範圍) ）。         |
| `MaxExtentsCreationTime`   | Datetime | 資料表中範圍的最大建立時間 (或 null （如果沒有任何範圍) ）。         |
| `RowOrderPolicy`           | String   | 資料表的有效資料列順序原則（序列化為 JSON）。                                     |

`*`將 *父實體的原則納入考慮 (例如資料庫/叢集) 。*

**輸出範例**

| TableName         | DatabaseName | 資料夾 | DocString | TotalExtents | TotalExtentSize | TotalOriginalSize | TotalRowCount | HotExtents | HotExtentSize | HotOriginalSize | HotRowCount | AuthorizedPrincipals                                                                                                                                                                               | >retentionpolicy                                                                                                                                       | CachingPolicy                                                                        | ShardingPolicy                                                                    | MergePolicy                                                                                                                                             | StreamingIngestionPolicy | MinExtentsCreationTime      | MaxExtentsCreationTime      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| Operations        | Operations   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | [{"Type"： "AAD User"，"DisplayName"： "My Name (upn： alias@fabrikam.com) "，"ObjectId"： "a7a77777-4c21-4649-95c5-350bf486087b"，"FQN"： "aaduser = a7a77777-4c21-4649-95c5-350bf486087b"，"Notes"： ""}] | {"SoftDeletePeriod"： "365.00：00： 00"，"ContainerRecyclingPeriod"： "1.00：00： 00"，"ExtentsDataSizeLimitInBytes"：0，"OriginalDataSizeLimitInBytes"： 0}  | {"DataHotSpan"： "4.00：00： 00"，"IndexHotSpan"： "4.00：00： 00"，"ColumnOverrides"： []} | {"MaxRowCount"：750000，"MaxExtentSizeInMb"：1024，"MaxOriginalSizeInMb"： 2048} | {"RowCountUpperBoundForMerge"：0，"MaxExtentsToMerge"：100，"LoopPeriod"： "01:00:00"，"MaxRangeInHours"：3，"AllowRebuild"： true，"AllowMerge"： true} | null                     |
| ServiceOperations | Operations   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | [{"Type"： "AAD User"，"DisplayName"： "My Name (upn： alias@fabrikam.com) "，"ObjectId"： "a7a77777-4c21-4649-95c5-350bf486087b"，"FQN"： "aaduser = a7a77777-4c21-4649-95c5-350bf486087b"，"Notes"： ""}] | {"SoftDeletePeriod"： "365.00：00： 00"，"ContainerRecyclingPeriod"： "1.00：00： 00"，"ExtentsDataSizeLimitInBytes"：0，"OriginalDataSizeLimitInBytes"： 0} | {"DataHotSpan"： "4.00：00： 00"，"IndexHotSpan"： "4.00：00： 00"，"ColumnOverrides"： []} | {"MaxRowCount"：750000，"MaxExtentSizeInMb"：1024，"MaxOriginalSizeInMb"： 2048} | {"RowCountUpperBoundForMerge"：0，"MaxExtentsToMerge"：100，"LoopPeriod"： "01:00:00"，"MaxRangeInHours"：3，"AllowRebuild"： true，"AllowMerge"： true} | null                     | 2018-02-08 15：30：38.8489786 | 2018-02-14 07：47：28.7660267 |
