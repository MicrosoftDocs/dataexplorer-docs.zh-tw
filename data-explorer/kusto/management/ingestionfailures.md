---
title: 內嵌失敗-Azure 資料總管
description: 本文說明 Azure 資料總管中的內嵌失敗。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: a7a2dcaea2ef982edc8286b83a042d2f21460986
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610435"
---
# <a name="ingestion-failures"></a>內嵌失敗

## <a name="show-ingestion-failures"></a>。顯示內嵌失敗


此命令會傳回結果集，其中包含當 [資料內嵌控制命令](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands) 執行時所發生的任何內嵌失敗。


> [!NOTE]
> 內嵌流程的其他部分期間發生的內嵌失敗，將不會出現在此命令的結果集中。 例如，在資料內嵌控制命令傳送至 Kusto 資料引擎服務之前，可能會發生這類失敗。

> 如需監視牽涉到 [佇列](../api/netfx/about-kusto-ingest.md#queued-ingestion)內嵌之流程中發生之失敗的詳細資訊，請參閱 [本指南](../api/netfx/kusto-ingest-client-status.md)。

**語法**

|語法選項|描述|
|---|---| 
|`.show` `ingestion` `failures`                                       |傳回所有記錄的內嵌失敗  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |傳回一組已篩選的內嵌失敗
|`.show``ingestion` `failures` `with(OperationId="`*OperationId*`")` |傳回特定作業識別碼的內嵌失敗

**結果**
 
|輸出參數           |類型     |描述                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |字串   |作業識別碼，可用來透過 <br> [。顯示作業](operations.md) 命令 </br> 
|資料庫                   |String   |發生失敗的資料庫
|Table                      |字串   |發生失敗的資料表
|FailedOn                   |Datetime |註冊失敗時的日期/時間 (UTC)  
|IngestionSourcePath        |字串   |識別內嵌來源 (通常是 Azure Blob URI)  
|詳細資料                    |字串   |失敗詳細資料。 提供實際內嵌失敗根本原因的深入解析
|FailureKind                |字串   |失敗 (永久/暫時性) 的類型
|RootActivityId             |字串   |根活動識別碼。
|OperationKind              |字串   |內嵌作業類型 (階段) 在這段期間註冊失敗
|OriginatesFromUpdatePolicy |Boolean | 指出執行[更新原則](update-policy.md)時是否已註冊失敗
 
**範例**
 
|OperationId |資料庫 |Table |FailedOn |IngestionSourcePath |詳細資料 |FailureKind |RootActivityId |OperationKind |OriginatesFromUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22：25：03.1147331 |...Url。。。 |識別碼為 ' * * * * * .csv ' 的串流有格式不正確的 CSV 格式 * |持續性 |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22：34：11.2565943 |...Url。。。 |識別碼為 ' * * * * * .csv ' 的串流有格式不正確的 CSV 格式 * |持續性 |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22：34：44.5824741 |...Url。。。 |識別碼為 ' * * * * * .csv ' 的串流有格式不正確的 CSV 格式 * |持續性 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22：36：26.5525250 |...Url。。。 |發生未知的錯誤：擲回類型 ' system.string ' 的例外狀況 |暫時性 |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23：52：31.5460071 |...Url。。。 |無法下載 blob：用戶端無法在指定的超時時間內完成作業 |持續性 |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0
