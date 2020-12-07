---
title: ：顯示資料庫-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中顯示資料庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: eb3e1dd16d36af5d92019b710f3d5790a24b1296
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320480"
---
# <a name="show-database"></a>.show 資料庫

傳回資料表，其中顯示內容資料庫的屬性。

若要傳回資料表，其中的每一筆記錄對應至使用者可存取的叢集中的資料庫，請參閱 [`.show databases`](show-databases.md) 。

**語法**

`.show` `database` [`details` | `identity` | `policies` | `datastats`]

未指定任何選項的預設呼叫會等於 ' identity ' 選項。

**' Identity ' 選項的輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱會區分大小寫。 
|PersistentStorage  |String |儲存資料庫的持續性儲存體 URI。  (暫時資料庫的這個欄位是空的。 )  
|版本  |String |資料庫版本號碼。 資料庫 (中的每個變更作業都會更新這個數位，例如新增資料和變更架構) 。 
|IsCurrent  |Boolean |如果資料庫是目前連接所指向的資料庫，則為 True。 
|DatabaseAccessMode  |String |叢集如何連接至資料庫。 例如，如果資料庫是以唯讀模式附加，則叢集將會使所有以任何方式修改資料庫的要求失敗。 
|PrettyName |String |資料庫的名稱。
|CurrentUserIsUnrestrictedViewer |Boolean | 指定目前的使用者是否為資料庫上不受限制的檢視器。
|DatabaseId |Guid |資料庫的唯一識別碼。

**[詳細資料] 選項的輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱會區分大小寫。 
|PersistentStorage  |String |儲存資料庫的持續性儲存體 URI。  (暫時資料庫的這個欄位是空的。 )  
|版本  |String |資料庫版本號碼。 資料庫 (中的每個變更作業都會更新這個數位，例如新增資料和變更架構) 。 
|IsCurrent  |Boolean |如果資料庫是目前連接所指向的資料庫，則為 True。 
|DatabaseAccessMode  |String |叢集如何連接至資料庫。 例如，如果資料庫是以唯讀模式附加，則叢集將會使所有以任何方式修改資料庫的要求失敗。 
|PrettyName |String |資料庫的名稱。
|AuthorizedPrincipals |String | 資料庫的授權主體集合 (以 JSON 格式) 進行序列化。
|>retentionpolicy |String | 資料庫的保留原則 (以 JSON 格式) 進行序列化。
|MergePolicy |String | 資料庫的範圍合併原則 (以 JSON 格式) 進行序列化。
|CachingPolicy |String | 資料庫的快取原則 (以 JSON 格式) 進行序列化。
|ShardingPolicy |String | 資料庫的分區化原則 (以 JSON 格式) 進行序列化。
|StreamingIngestionPolicy |String | 資料庫的串流內嵌原則 (以 JSON 格式) 進行序列化。
|IngestionBatchingPolicy |String | 資料庫的內嵌批次處理原則 (以 JSON 格式) 進行序列化。
|TotalSize |Real | 資料庫的範圍總計大小。
|DatabaseId |Guid |資料庫的唯一識別碼。

**[原則] 選項的輸出**
 
|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱會區分大小寫。 
|PersistentStorage  |String |儲存資料庫的持續性儲存體 URI。  (暫時資料庫的這個欄位是空的。 )  
|版本  |String |資料庫版本號碼。 資料庫 (中的每個變更作業都會更新這個數位，例如新增資料和變更架構) 。 
|IsCurrent  |Boolean |如果資料庫是目前連接所指向的資料庫，則為 True。 
|DatabaseAccessMode  |String |叢集如何連接至資料庫。 例如，如果資料庫是以唯讀模式附加，則叢集將會使所有以任何方式修改資料庫的要求失敗。 
|PrettyName |String |資料庫的很好名稱。
|DatabaseId |Guid |資料庫的唯一識別碼。
|AuthorizedPrincipals |String | 資料庫的授權主體集合 (以 JSON 格式) 進行序列化。
|>retentionpolicy |String | 資料庫的保留原則 (以 JSON 格式) 進行序列化。
|MergePolicy |String | 資料庫的範圍合併原則 (以 JSON 格式) 進行序列化。
|CachingPolicy |String | 資料庫的快取原則 (以 JSON 格式) 進行序列化。
|ShardingPolicy |String | 資料庫的分區化原則 (以 JSON 格式) 進行序列化。
|StreamingIngestionPolicy |String | 資料庫的串流內嵌原則 (以 JSON 格式) 進行序列化。
|IngestionBatchingPolicy |String | 資料庫的內嵌批次處理原則 (以 JSON 格式) 進行序列化。

**' Datastats ' 選項的輸出**

|輸出參數 |類型 |描述 
|---|---|---
|DatabaseName  |String |資料庫名稱。 資料庫名稱會區分大小寫。
|PersistentStorage  |String |儲存資料庫的持續性儲存體 URI。  (暫時資料庫的這個欄位是空的。 )  
|版本  |String |資料庫版本號碼。 資料庫 (中的每個變更作業都會更新這個數位，例如新增資料和變更架構) 。 
|IsCurrent  |Boolean |如果資料庫是目前連接所指向的資料庫，則為 True。 
|DatabaseAccessMode  |String |叢集如何連接至資料庫。 例如，如果資料庫是以唯讀模式附加，則叢集將會使所有以任何方式修改資料庫的要求失敗。 
|PrettyName |String |資料庫的很好名稱。
|DatabaseId |Guid |資料庫的唯一識別碼。
|OriginalSize |Real | 資料庫的範圍總計的原始大小。
|ExtentSize |Real | 資料庫的範圍總計大小 (資料 + 索引) 。
|CompressedSize |Real | 資料庫的範圍總計資料壓縮大小。
|IndexSize |Real | 資料庫的範圍總計的索引大小。
|RowCount |long | 資料庫的範圍總計資料列計數。
|HotOriginalSize |Real | 資料庫的最大範圍總計的原始大小。
|HotExtentSize |Real | 資料庫的作用區總計大小 (資料 + 索引) 。
|HotCompressedSize |Real | 資料庫的最忙碌範圍總計資料壓縮大小。
|HotIndexSize |Real | 資料庫的作用區總計索引大小。
|HotRowCount |long | 資料庫的作用區總計資料列計數。