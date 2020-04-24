---
title: 引入失敗 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的引入失敗。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 699fd01e9a284179bf2749b58581392d2170f0ca
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520939"
---
# <a name="ingestion-failures"></a>攝入失敗

## <a name="show-ingestion-failures"></a>.顯示引入失敗

此命令返回一個結果集,其中包括在執行[數據引入控制命令](data-ingestion/index.md)期間遇到的所有引入失敗。

*注意*： 
- 引入流的其他部分(例如,在將數據引入控制命令發送到 Kusto 的數據引擎服務之前)時遇到的引入失敗不會出現在此命令的結果集中。
- 對於監視在涉及[排隊引入](../api/netfx/about-kusto-ingest.md#queued-ingestion)的流中發生的故障,建議遵循[本指南](../api/netfx/kusto-ingest-client-status.md)。

**語法**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |傳回所有紀錄的引入失敗  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |傳回一組篩選的引入失敗
|`.show``ingestion``with(OperationId="``failures`操作*OperationId*代碼`")` |傳回指定的操作代碼的引入失敗

**結果**
 
|輸出參數 |類型 |描述 
|---|---|---
|操作代碼 |String |操作標識碼。 此參數可用於執行[.show 操作](operations.md)指令檢視其他操作詳細資訊 
|資料庫 |String |遇到問題的資料庫
|Table |String |遇到問題的表
|失敗開啟 |Datetime |註冊故障的日期/時間(以 UTC 表示) 
|引入來源路徑 |String |識別引入來源(通常為 Azure Blob URI) 
|詳細資料 |String |故障詳細資訊。 提供對實際攝入失敗根本原因的洞察
|失敗金德 |String |容錯型態 (永久/ 瞬態 )
|RootActivityId |String |根活動識別碼。
|動作金德 |String |註冊故障的引入操作型態(階段)
|源自更新政策 |Boolean | 指示在執行[更新策略](update-policy.md)時是否註冊了故障
 
**範例**
 
|操作代碼 |資料庫 |Table |失敗開啟 |引入來源路徑 |詳細資料 |失敗金德 |RootActivityId |動作金德 |源自更新政策
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395失聰 |DB1 |Table1 |2017-02-14 22:25:03.1147331 |...Url。。。 |具有 id'_.csv'的流格式格式不正確 |持續性 |3c883942-e446-4999-9b00-d4c664f06ef6 |資料擷取 | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22:34:11.2565943 |...Url。。。 |具有 id'_.csv'的流格式格式不正確 |持續性 |48571bdb-b714-4f32-8ddc-4001838a956c |資料擷取 | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22:34:44.5824741 |...Url。。。 |具有 id'_.csv'的流格式格式不正確 |持續性 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |資料擷取 | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22:36:26.5525250 |...Url。。。 |發生未知錯誤:引發類型「系統.例外」的異常 |暫時性 |9b7bb017-471e-48f6-9c96-d16fcf938d2a |資料擷取 | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23:52:31.5460071 |...Url。。。 |下載 Blob 失敗:客戶端無法在指定的超時內完成操作 |持續性 |21fa0d6-cd7d-4493-b6f7-78916ce0d617 |資料擷取 | 0