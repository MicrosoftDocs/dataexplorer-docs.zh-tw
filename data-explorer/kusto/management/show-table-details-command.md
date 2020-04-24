---
title: .顯示表詳細資訊 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 .顯示 Azure 數據資源管理器中的表詳細資訊。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 6197e173df417acb1f5dc506337773f9534564bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519783"
---
# <a name="show-table-details"></a>.顯示表詳細資訊
返回包含指定表或資料庫中所有表的集,其中包含每個表屬性的詳細摘要。

需要[資料庫檢視器權限](../management/access-control/role-based-authorization.md)。

```
.show table T1 details
.show tables (T1, ..., Tn) details
.show tables details
```

**輸出**

| 輸出參數           | 類型     | 描述                                                                                     |
|----------------------------|----------|-------------------------------------------------------------------------------------------------|
| `TableName`                | String   | 資料表的名稱。                                                                          |
| `DatabaseName`             | String   | 表所屬的資料庫。                                                         |
| `Folder`                   | String   | 表的資料夾。                                                                             |
| `DocString`                | String   | 記錄表的字串。                                                                 |
| `TotalExtents`             | Int64    | 錶中的擴展盤區總數。                                                       |
| `TotalExtentSize`          | Double   | 表中範圍的總大小(壓縮大小 + 索引大小)。                          |
| `TotalOriginalSize`        | Double   | 表中數據的總原始大小。                                                   |
| `TotalRowCount`            | Int64    | 表中的行總數。                                                          |
| `HotExtents`               | Int64    | 表中存儲在熱緩存中的擴展盤區總數。                              |
| `HotExtentSize`            | Double   | 儲存在熱緩存中的擴展盤區的總大小(壓縮大小 + 索引大小)。 |
| `HotOriginalSize`          | Double   | 存儲在熱緩存中的數據的總原始大小。                          |
| `HotRowCount`              | Int64    | 存儲在熱緩存中的表中的行總數。                                 |
| `AuthorizedPrincipals`     | String   | 該表的授權主體,序列化為 JSON。                                          |
| `RetentionPolicy`          | String   | 該表的有效`*`保留策略,序列化為 JSON。                                  |
| `CachingPolicy`            | String   | 該表的有效`*`緩存策略,序列化為 JSON。                                    |
| `ShardingPolicy`           | String   | 該表的有效`*`分片策略,序列化為 JSON.6666666666666666666666666666666666666666                     |
| `MergePolicy`              | String   | 該表的有效`*`合併策略,序列化為 JSON。                                      |
| `StreamingIngestionPolicy` | String   | 該表的有效`*`流式處理策略,序列化為 JSON。                        |
| `MinExtentsCreationTime`   | Datetime | 表中範圍的最小創建時間(如果沒有擴展盤區則為null)。         |
| `MaxExtentsCreationTime`   | Datetime | 表中範圍的最大創建時間(如果沒有擴展盤區則為null)。         |
| `RowOrderPolicy`           | String   | 該表的有效行訂單策略,序列化為 JSON。                                     |

`*`*考慮父實體(如資料庫/群集)的策略。*

**輸出範例**

| TableName         | DatabaseName | 資料夾 | 文件字串 | 總範圍 | 總範圍大小 | 總原始大小 | 總行計數 | 熱範圍 | 熱範圍大小 | 熱原裝尺寸 | 熱羅計數 | 授權委託人                                                                                                                                                                               | 保留政策                                                                                                                                       | 快取政策                                                                        | 分片原則                                                                    | 合併原則                                                                                                                                             | 串流化原則 | 最小範圍創造時間      | 最大範圍的創造時間      |
|-------------------|--------------|--------|-----------|--------------|-----------------|-------------------|---------------|------------|---------------|-----------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|-----------------------------|-----------------------------|
| 作業        | 作業   |        |           | 1164         | 37687203        | 53451358          | 223325        | 29         | 838752        | 1388213         | 5117        | {"類型":"AAD 使用者","顯示名稱":"我的名字(上名:)", alias@fabrikam.com"ObjectId":"a7a77777-4c21-4649-95c5-350bf486087b","FQN":"aaduser_a7a77777-4c21-4649-95c5-350bf486087b","注":""*" | {"軟刪除週期":"365.00:00:00","容器回收期":"1.00:00:00","範圍大小限制位元組":0,"原始數據大小限制位元組":0 |  | • "DataHotSpan":"4.00:00:00","IndexHotSpan":"4.00:00:00","柱子覆蓋":"[] | • "最大行計數":750000,"最大範圍大小值":1024,"最大原始大小值":2048 | | • "RowCountupperBundFormerge":0,"最大範圍":100,"循環週期":"01:00:00","最大時間":3,"允許重建":true,"允許合併":真實| | null                     |
| ServiceOperations | 作業   |        |           | 1109         | 76588803        | 91553069          | 110125        | 27         | 2635742       | 2929926         | 3162        | {"類型":"AAD 使用者","顯示名稱":"我的名字(上名:)", alias@fabrikam.com"ObjectId":"a7a77777-4c21-4649-95c5-350bf486087b","FQN":"aaduser_a7a77777-4c21-4649-95c5-350bf486087b","注":""*" | * "軟刪除週期":"365.00:00:00","容器回收期":"1.00:00:00","範圍大小限制位元組":0,"原始數據大小限制位元組":0 | | • "DataHotSpan":"4.00:00:00","IndexHotSpan":"4.00:00:00","柱子覆蓋":"[] | • "最大行計數":750000,"最大範圍大小值":1024,"最大原始大小值":2048 | | • "RowCountupperBundFormerge":0,"最大範圍":100,"循環週期":"01:00:00","最大時間":3,"允許重建":true,"允許合併":真實| | null                     | 2018-02-08 15:30:38.8489786 | 2018-02-14 07:47:28.7660267 |