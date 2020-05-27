---
title: Operations management-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的作業管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 40103d399feb61e994639c9447510ef90fef652d
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011460"
---
# <a name="operations-management"></a>Operations Management

## <a name="show-operations"></a>。顯示作業

`.show``operations`命令會傳回一個資料表，其中包含在過去兩周（目前為保留週期設定）中執行和已完成的所有系統管理作業。

**語法**

|||
|---|---| 
|`.show` `operations`              |傳回叢集正在處理的所有作業，或叢集已處理的作業
|`.show``operations` *OperationId*|傳回特定識別碼的作業狀態 
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...）|傳回特定識別碼的作業狀態

**結果**
 
|輸出參數 |類型 |描述
|---|---|---
|識別碼 |String |作業識別碼
|作業 |String |管理員命令別名
|NodeId |String |如果命令有遠端執行（例如，DataIngestPull）-，則會包含執行中遠端節點的識別碼。
|StartedOn |Datetime |作業開始的日期/時間（UTC）
|LastUpdatedOn |Datetime |上次更新作業時的日期/時間（UTC）（可以是作業內的步驟，或完成步驟）
|持續時間 |Datetime |LastUpdateOn 與 StartedOn 之間的 TimeSpan
|State |String |命令狀態-可以是 "InProgress"、"Completed" 或 "Failed" 的值
|狀態 |String |包含失敗作業錯誤的其他說明字串
 
**範例**
 
|識別碼 |作業 |節點識別碼 |開始時間 |上次更新日期 |持續時間 |State |狀態 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08：47：01.0000000 |2015-01-06 08：47：01.0000000 |0001-01-01 00：00：00.0000000 |已完成 |
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto。 Svc_IN_1 |2015-01-06 08：47：02.0000000 |2015-01-06 08：48：19.0000000 |0001-01-01 00：01：17.0000000 |已完成 |
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08：47：18.0000000 |2015-01-06 08：47：18.0000000 |0001-01-01 00：00：00.0000000 |已完成 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08：41：01.0000000 |0001-01-01 00：00：00.0000000 |0001-01-01 00：00：00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14：57：41.0000000 |2015-01-10 14：57：41.0000000 |0001-01-01 00：00：00.0000000 |Failed |集合已修改。 列舉運算可能無法執行。

## <a name="show-operation-details"></a>。顯示作業詳細資料

作業可以（選擇性地）保存其結果，並使用來執行操作完成時，可以抓取結果 `.show` `operation` `details` 。

> [!NOTE]
> 並非所有的控制項命令都會保存其結果。 這些命令通常會在非同步執行時，使用關鍵字進行預設的動作 `async` 。 如需詳細資訊，請參閱命令特有的檔。 例如，請參閱[資料匯出](data-export/index.md)）。
> 命令的輸出架構 `.show` `operations` `details` 是從同步執行命令所傳回的相同架構。
> 只有在作業 `.show` `operation` `details` 成功完成後，才能叫用此命令。 在執行此命令之前，請使用 [[顯示作業] 命令](#show-operations)來檢查操作的狀態。

**語法**

`.show``operation` *OperationId*`details`

**結果**

每種作業類型的結果都不同，而且會在同步執行時符合作業結果的架構。

**範例**

範例中的*OperationId*會從其中一個[資料匯出](../management/data-export/index.md)命令的非同步執行傳回。

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```

Async export 命令傳回下列作業識別碼：

|OperationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

此作業識別碼可在命令完成以查詢匯出的 blob 時使用。 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**結果**

|路徑|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|
