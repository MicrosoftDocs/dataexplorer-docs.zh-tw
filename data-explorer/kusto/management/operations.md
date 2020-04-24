---
title: 操作管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的操作管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 01f30a8d391948d5466ef76b2951d55aa6084e15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520684"
---
# <a name="operations-management"></a>Operations Management

## <a name="show-operations"></a>.顯示操作

`.show``operations`命令返回一個表,該表具有在過去兩周內執行的所有管理操作(無論是運行操作還是已完成操作),當前是保留期配置。

**語法**

|||
|---|---| 
|`.show` `operations`              |傳回群組已處理或處理的所有操作 
|`.show``operations`*操作代碼*|傳回特定識別碼的作業狀態 
|`.show`*OperationId1*`(`操作代碼代碼代碼2 ...) `operations` `,` * * `,`|傳回指定的指示的作業狀態

**結果**
 
|輸出參數 |類型 |描述 
|---|---|---
|Id |String |操作標識碼。
|作業 |String |管理員命令別名 
|NodeId |String |如果指令具有遠端執行 (例如資料化)- NodeId 將包含正在執行的遠端節點的 ID 
|啟動 |Datetime |動作開始的日期/時間(以 UTC 表示) 
|上次更新時間 |Datetime |上次更新操作的日期/時間(以 UTC 表示)(可以是操作內的一個步驟,也可以是完成步驟) 
|Duration |Datetime |上次更新開啟與啟動時間之間的時間跨度 
|State |String |命令狀態:可以具有「進度」、「已完成」或「失敗」的值 
|狀態 |String |其他說明字串,其中一個儲存失敗操作的錯誤 
 
**範例**
 
|Id |作業 |節點識別碼 |已啟動 |上次更新時間 |Duration |State |狀態 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395失聰 |架構顯示 | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Completed | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |資料擷取 |庫斯托.Azure.Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Completed | 
|e198c519-5263-4629-a158-8d68f7a1022f |操作顯示 | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Completed | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |範圍刪除 | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress | 
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |資料擷取 | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |失敗 |集合已修改;枚舉操作可能無法執行。 

## <a name="show-operation-details"></a>.顯示操作詳細資訊

操作可以(可選)保留其結果,並且在操作完成後,可以使用`.show``operation``details` 

**注意：**

* 並非所有控制命令都保留其結果,並且默認情況下僅在非同步執行(使用關鍵字`async`)上執行這些命令。 請搜索特定命令的文檔,並檢查是否這樣做(例如[,請參閱資料匯出](data-export/index.md))。 
* 命令的`.show``operations``details`輸出架構是命令同步執行返回的相同架構。 
* 只有在操作成功完成之後才能調用`details`該命令`.show``operation`。 在呼叫此指令之前,使用[show 操作命令](#show-operations)() 檢查操作的狀態。 

**語法**

`.show``operation`*操作代碼*`details`

**結果**

同步執行時,結果因操作類型而異,並且與操作結果的架構匹配。 

**範例**

此示例中的*操作 Id*是從其中一個[數據匯出](../management/data-export/index.md)命令的非同步執行返回的。

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```
同步匯出指令傳回以下操作 ID:

|操作代碼|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

當命令完成查詢匯出的 Blob 時,可以使用此操作 ID,如下所示: 

```
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**結果**

|Path|數位記錄|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|