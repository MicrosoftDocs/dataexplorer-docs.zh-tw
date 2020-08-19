---
title: 作業管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的作業管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9c25900817aadcc450696525be8446d385d2e220
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610469"
---
# <a name="operations-management"></a>Operations Management

## <a name="show-operations"></a>。顯示作業 

`.show``operations`命令會傳回一份資料表，其中包含執行中和已完成的所有管理作業，這些作業都是在過去兩周內執行 (目前的保留期限設定) 。

**語法**

|語法選項|描述|
|---|---| 
|`.show` `operations`              |傳回叢集正在處理的所有作業，或叢集已處理的作業
|`.show``operations` *OperationId*|傳回特定識別碼的作業狀態 
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ... ) |傳回特定識別碼的作業狀態

**結果**
 
|輸出參數 |類型 |描述
|---|---|---
|ID |字串 |作業識別碼
|作業 |字串 |管理命令別名
|NodeId |字串 |如果命令具有遠端執行 (例如，DataIngestPull) -節點將包含執行遠端節點的識別碼。
|StartedOn |Datetime |作業開始時的日期/時間 (UTC) 
|LastUpdatedOn |Datetime |上次更新作業時的日期/時間 ()  (可以是作業中的步驟或完成步驟) 
|Duration |Datetime |LastUpdateOn 與 StartedOn 之間的 TimeSpan
|狀態 |字串 |命令狀態-可以有 "InProgress"、"Completed" 或 "Failed" 的值
|狀態 |字串 |包含失敗作業錯誤的其他說明字串
 
**範例**
 
|ID |作業 |節點識別碼 |開始時間 |上次更新時間 |Duration |狀態 |狀態 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08：47：01.0000000 |2015-01-06 08：47：01.0000000 |0001-01-01 00：00：00.0000000 |已完成 |
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto。 Svc_IN_1 |2015-01-06 08：47：02.0000000 |2015-01-06 08：48：19.0000000 |0001-01-01 00：01：17.0000000 |已完成 |
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08：47：18.0000000 |2015-01-06 08：47：18.0000000 |0001-01-01 00：00：00.0000000 |已完成 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08：41：01.0000000 |0001-01-01 00：00：00.0000000 |0001-01-01 00：00：00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14：57：41.0000000 |2015-01-10 14：57：41.0000000 |0001-01-01 00：00：00.0000000 |失敗 |集合已修改。 列舉運算可能無法執行。

## <a name="show-operation-details"></a>。顯示作業詳細資料

作業可以 (選擇性地) 保存其結果，而且可以在作業完成時，使用來取出結果 `.show` `operation` `details` 。

> [!NOTE]
> 並非所有控制命令都會保存其結果。 這樣做的命令通常只會在非同步執行時使用 `async` 關鍵字。 請參閱特定命令的檔，並檢查其是否存在。 例如，請參閱 [資料匯出](data-export/index.md)) 。
> 命令的輸出架構與 `.show` `operations` `details` 命令的同步執行所傳回的架構相同。
> 只有在作業 `.show` `operation` `details` 成功完成之後，才能叫用此命令。 執行此命令之前，請使用 [ [顯示作業] 命令](#show-operations)) 檢查作業的狀態。

**語法**

`.show``operation` *OperationId*`details`

**結果**

每種類型的作業都會有不同的結果，並且會在同步執行時符合作業結果的架構。

**範例**

範例中的 *OperationId* 會從非同步執行其中一個 [資料匯出](../management/data-export/index.md) 命令傳回。

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 
```

非同步匯出命令傳回下列作業識別碼：

|OperationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

當命令完成時，可以使用此作業識別碼來查詢匯出的 blob。 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

|路徑|NumRecords |
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|
