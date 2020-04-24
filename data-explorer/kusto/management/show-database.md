---
title: .顯示資料庫 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .show 資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 95d90377afd33971052dd00cd6ab3d662130824c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519868"
---
# <a name="show-database"></a>.顯示資料庫

返回顯示上下文資料庫屬性的表。

要傳回表,其中每個紀錄對應於使用者有權存取的群組的資料庫,請參閱[.show 資料庫](show-databases.md)。

**語法**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

未指定任何選項的預設呼叫等於"標識"選項。

**輸出「識別」選項**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱區分大小寫。 
|持久存儲  |String |存儲資料庫的持久存儲URI。 (此欄位對於臨時資料庫為空。 
|版本  |String |資料庫版本號碼。 對於資料庫中的每個更改操作(如添加數據和更改架構),此數位將更新。 
|IsCurrent  |Boolean |如果資料庫是當前連接指向的,則為 True。 
|資料庫存取模式  |String |群集如何附加到資料庫。 例如,如果資料庫以 ReadOnly 模式附加,則群集將失敗所有以任何方式修改資料庫的請求。 
|漂亮名稱 |String |資料庫名稱相當。
|目前使用者沒有限制檢視器 |Boolean | 指定當前使用者是否為資料庫上的無限制查看器。
|DatabaseId |Guid |資料庫的唯一 ID。

**輸出「詳細資訊」選項**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱區分大小寫。 
|持久存儲  |String |存儲資料庫的持久存儲URI。 (此欄位對於臨時資料庫為空。 
|版本  |String |資料庫版本號。 對於資料庫中的每個更改操作(如添加數據和更改架構),此數位將更新。 
|IsCurrent  |Boolean |如果資料庫是當前連接指向的,則為 True。 
|資料庫存取模式  |String |群集如何附加到資料庫。 例如,如果資料庫以 ReadOnly 模式附加,則群集將失敗所有以任何方式修改資料庫的請求。 
|漂亮名稱 |String |資料庫名稱相當。
|授權委託人 |String | 資料庫的授權主體集合(以 JSON 格式序列化)。
|保留政策 |String | 資料庫的保留策略(以 JSON 格式序列化)。
|合併原則 |String | 資料庫的擴展區合併策略(以 JSON 格式序列化)。
|快取政策 |String | 資料庫的緩存策略(以 JSON 格式序列化)。
|分片原則 |String | 資料庫的分片策略(以 JSON 格式序列化)。
|串流化原則 |String | 資料庫的流式處理策略(以 JSON 格式序列化)。
|引入批次處理原則 |String | 資料庫的引入批處理策略(以 JSON 格式序列化)。
|TotalSize |Real | 資料庫的擴展區總大小。
|DatabaseId |Guid |資料庫的唯一 ID。

**輸出「政策」選項**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱區分大小寫。 
|持久存儲  |String |存儲資料庫的持久存儲URI。 (此欄位對於臨時資料庫為空。 
|版本  |String |資料庫版本號。 對於資料庫中的每個更改操作(如添加數據和更改架構),此數位將更新。 
|IsCurrent  |Boolean |如果資料庫是當前連接指向的,則為 True。 
|資料庫存取模式  |String |群集如何附加到資料庫。 例如,如果資料庫以 ReadOnly 模式附加,則群集將失敗所有以任何方式修改資料庫的請求。 
|漂亮名稱 |String |資料庫的名字很漂亮。
|DatabaseId |Guid |資料庫的唯一 ID。
|授權委託人 |String | 資料庫的授權主體集合(以 JSON 格式序列化)。
|保留政策 |String | 資料庫的保留策略(以 JSON 格式序列化)。
|合併原則 |String | 資料庫的擴展區合併策略(以 JSON 格式序列化)。
|快取政策 |String | 資料庫的緩存策略(以 JSON 格式序列化)。
|分片原則 |String | 資料庫的分片策略(以 JSON 格式序列化)。
|串流化原則 |String | 資料庫的流式處理策略(以 JSON 格式序列化)。
|引入批次處理原則 |String | 資料庫的引入批處理策略(以 JSON 格式序列化)。

**輸出資料統計選項**

|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱區分大小寫。
|持久存儲  |String |存儲資料庫的持久存儲URI。 (此欄位對於臨時資料庫為空。 
|版本  |String |資料庫版本號。 對於資料庫中的每個更改操作(如添加數據和更改架構),此數位將更新。 
|IsCurrent  |Boolean |如果資料庫是當前連接指向的,則為 True。 
|資料庫存取模式  |String |群集如何附加到資料庫。 例如,如果資料庫以 ReadOnly 模式附加,則群集將失敗所有以任何方式修改資料庫的請求。 
|漂亮名稱 |String |資料庫的名字很漂亮。
|DatabaseId |Guid |資料庫的唯一 ID。
|原始大小 |Real | 資料庫的擴展區的總原始大小。
|範圍大小 |Real | 資料庫的擴展區總大小(數據 + 索引)。
|壓縮大小 |Real | 資料庫的擴展區的總數據壓縮大小。
|IndexSize |Real | 資料庫的擴展區總索引大小。
|RowCount |long | 資料庫的擴展區的總行計數。
|熱原裝尺寸 |Real | 資料庫的熱範圍的總原始大小。
|熱範圍大小 |Real | 資料庫的熱範圍總大小(數據 + 索引)。
|熱壓縮大小 |Real | 資料庫的熱範圍的總數據壓縮大小。
|熱指數大小 |Real | 資料庫的熱範圍的總索引大小。
|熱羅計數 |long | 資料庫的熱範圍總行計數。