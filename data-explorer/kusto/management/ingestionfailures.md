---
title: 內嵌失敗-Azure 資料總管
description: 本文說明 Azure 資料總管中的內建失敗。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/20/2019
ms.openlocfilehash: 7684ea11b03113051580e3e19aef0d9ac3f13585
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011477"
---
# <a name="ingestion-failures"></a>內嵌失敗

## <a name="show-ingestion-failures"></a>。顯示內嵌失敗


此命令會傳回結果集，其中包含[資料內嵌控制項命令](../../ingest-data-overview.md#kusto-query-language-ingest-control-commands)執行時所發生的任何內嵌失敗。


> [!NOTE]
> 在內嵌流程的其他部分期間所發生的內嵌失敗，將不會出現在此命令的結果集中。 例如，在資料內嵌控制命令傳送至 Kusto 資料引擎服務之前，可能會發生這類失敗。

> 如需監視涉及[佇列](../api/netfx/about-kusto-ingest.md#queued-ingestion)內嵌之流程中發生之失敗的詳細資訊，請參閱[本指南](../api/netfx/kusto-ingest-client-status.md)。

**語法**

|||
|---|---| 
|`.show` `ingestion` `failures`                                       |傳回所有記錄的內嵌失敗  
|`.show` `ingestion` `failures` <code>&#124;</code> `where` ...       |傳回篩選過的內嵌失敗集合
|`.show``ingestion` `failures` `with(OperationId="`*OperationId*`")` |傳回特定作業識別碼的內嵌失敗

**結果**
 
|輸出參數           |類型     |描述                                                                              |
|---------------------------|---------|-----------------------------------------------------------------------------------------|
|OperationId                |String   |作業識別碼，可以用來透過 <br> [。顯示作業](operations.md)命令 </br> 
|資料庫                   |String   |發生失敗的資料庫
|Table                      |String   |發生失敗的資料表
|FailedOn                   |Datetime |註冊失敗時的日期/時間（UTC） 
|IngestionSourcePath        |String   |識別內嵌來源（通常是 Azure Blob URI） 
|詳細資料                    |String   |失敗詳細資料。 提供實際內嵌失敗根本原因的深入解析
|FailureKind                |String   |失敗的類型（永久/暫時性）
|RootActivityId             |String   |根活動識別碼。
|OperationKind              |String   |在其中註冊失敗的內嵌操作類型（階段）
|OriginatesFromUpdatePolicy |布林值 | 指出執行[更新原則](update-policy.md)時是否已註冊失敗
 
**範例**
 
|OperationId |資料庫 |Table |FailedOn |IngestionSourcePath |詳細資料 |FailureKind |RootActivityId |OperationKind |OriginatesFromUpdatePolicy
|--|--|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |DB1 |Table1 |2017-02-14 22：25：03.1147331 |...url .。。 |識別碼為 ' * * * * * .csv ' 的資料流程具有格式不正確的 CSV 格式 * |持續性 |3c883942-e446-4999-9b00-d4c664f06ef6 |DataIngestPull | 0
|841fafa4-076a-4cba-9300-4836da0d9c75 |DB1 |Table1 |2017-02-14 22：34：11.2565943 |...url .。。 |識別碼為 ' * * * * * .csv ' 的資料流程具有格式不正確的 CSV 格式 * |持續性 |48571bdb-b714-4f32-8ddc-4001838a956c |DataIngestPull | 0
|e198c519-5263-4629-a158-8d68f7a1022f |DB1 |Table1 |2017-02-14 22：34：44.5824741 |...url .。。 |識別碼為 ' * * * * * .csv ' 的資料流程具有格式不正確的 CSV 格式 * |持續性 |5e31ab3c-e2c7-489a-827e-e89d2d691ec4 |DataIngestPull | 0
|a9f287a1-f3e6-4154-ad18-b86438da0929 |DB1 |Table1 |2017-02-14 22：36：26.5525250 |...url .。。 |發生未知的錯誤：擲回 ' system.exception ' 類型的例外狀況 |暫時性 |9b7bb017-471e-48f6-9c96-d16fcf938d2a |DataIngestPull | 0
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DB1 |Table1 |2017-02-14 23：52：31.5460071 |...url .。。 |無法下載 blob：用戶端無法在指定的時間內完成操作 |持續性 |21fa0dd6-cd7d-4493-b6f7-78916ce0d617 |DataIngestPull | 0
