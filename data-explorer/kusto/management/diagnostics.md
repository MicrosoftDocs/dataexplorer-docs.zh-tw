---
title: 診斷資訊-Azure 資料總管
description: 本文說明 Azure 資料總管中的診斷資訊。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d106d150ca7041c49b4d8eeaa703681c5f461fa8
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321534"
---
# <a name="diagnostic-information"></a>診斷資訊

這些命令可用於顯示系統診斷資訊。

* [`.show cluster`](#show-cluster)
* [`.show diagnostics`](#show-diagnostics)
* [`.show capacity`](#show-capacity)
* [`.show operations`](#show-operations)

## <a name="show-cluster"></a>。顯示叢集

```kusto
.show cluster
```

傳回集合，其中包含叢集中目前作用中之每個節點的一筆記錄。  

**結果** 

|輸出資料行 |類型 |描述
|---|---|---|
|NodeId|String|識別節點。 如果叢集已部署在 Azure 中，節點識別碼就是節點的 Azure RoleId
|位址|String |叢集用於節點間通訊的內部端點
|Name |String |節點的內部名稱。 名稱包括電腦名稱稱、進程名稱和處理序識別碼
|StartTime |Datetime |以) UTC 表示的日期/時間 (，表示節點中目前的 Kusto 具現化已開始。 此值可用來偵測節點) 上執行的節點 (或 Kusto 是否最近重新開機
|IsAdmin |Boolean |如果此節點目前為叢集的「領導者」 
|MachineTotalMemory  |Int64 |節點的 RAM 數量。
|MachineAvailableMemory  |Int64 |目前可用於節點的 RAM 數量。
|ProcessorCount  |Int32 |節點上的處理器數目。
|EnvironmentDescription  |字串 |將節點的環境序列化為 JSON 的其他相關資訊。 例如，as 升級/容錯網域

**範例**

```kusto
.show cluster
```

NodeID|位址|名稱|StartTime|IsAdmin|MachineTotalMemory|MachineAvailableMemory|ProcessorCount|EnvironmentDescription
---|---|---|---|---|---|---|---|---
Kusto.Azure.Svc_IN_1|net.tcp：//100.112.150.30： 23107/|Kusto.Azure.Svc_IN_4/RD000D3AB1E9BD/WaWorkerHost/3820|2016-01-15 02：00：22.6522152|True|274877435904|247797796864|16|{"UpdateDomain"：0，"FaultDomain"： 0}
Kusto.Azure.Svc_IN_3|net.tcp：//100.112.154.34： 23107/|Kusto.Azure.Svc_IN_3/RD000D3AB1E062/WaWorkerHost/2760|2016-01-15 05：52：52.1434683|False|274877435904|258740346880|16|{"UpdateDomain"：1，"FaultDomain"： 1}
Kusto.Azure.Svc_IN_2|net.tcp：//100.112.128.40： 23107/|Kusto.Azure.Svc_IN_2/RD000D3AB1E054/WaWorkerHost/3776|2016-01-15 07：17：18.0699790|False|274877435904|244232339456|16|{"UpdateDomain"：2，"FaultDomain"： 2}
Kusto.Azure.Svc_IN_0|net.tcp：//100.112.138.15： 23107/|Kusto.Azure.Svc_IN_0/RD000D3AB0D6C6/WaWorkerHost/3208|2016-01-15 09：46：36.9865016|False|274877435904|238414581760|16|{"UpdateDomain"：3，"FaultDomain"： 3}


## <a name="show-diagnostics"></a>。顯示診斷

```kusto
.show diagnostics
```

傳回健全狀況 Kusto 叢集狀態的相關資訊。
 
**傳回**

|輸出參數 |類型 |描述|
|-----------------|-----|-----------| 
|IsHealthy|Boolean|如果叢集狀況良好
|IsScaleOutRequired|Boolean|如果應藉由新增更多計算節點來增加叢集大小
|MachinesTotal|Int64|叢集中的機器數目
|MachinesOffline|Int64|目前離線的電腦數目
|NodeLastRestartedOn|Datetime|叢集中任何節點重新開機的最後一個日期/時間
|AdminLastElectedOn|Datetime|叢集管理員角色的最後一個日期/時間擁有權已變更
|MemoryLoadFactor|Double|叢集保留的資料量（相對於其最大容量100.0）
|ExtentsTotal|Int64|叢集目前在所有資料庫和所有資料表中所擁有的資料範圍總數
|保留|Int64|
|保留|Int64|
|InstancesTargetBasedOnDataCapacity|Int64|將 ClusterDataCapacityFactor 置於80以下所需的實例數目。 只有當所有機器的大小都相同時，此值才有效
|TotalOriginalDataSize|Int64|原始內嵌資料的大小總計（以位元組為單位）
|TotalExtentSize|Int64|壓縮和索引之後的儲存資料大小總計（以位元組為單位）
|IngestionsLoadFactor|Double|使用的叢集內嵌容量百分比。 使用命令可看到的最大容量 `.show capacity`
|IngestionsInProgress|Int64|目前正在完成的內嵌作業數目
|IngestionsSuccessRate|Double|在前10分鐘內成功完成的內嵌作業百分比
|MergesInProgress|Int64|目前正在進行的範圍合併作業數目
|BuildVersion|String|部署到叢集的 Kusto 軟體版本
|BuildTime|Datetime|Kusto 軟體組建版本的日期/時間。
|ClusterDataCapacityFactor|Double|使用的叢集資料容量百分比。 百分比會計算為 SUM (區大小資料) /SUM (SSD 快取大小) 。
|IsDataWarmingRequired|Boolean|內部：如果應該執行叢集的預熱查詢，將資料帶入本機 SSD 快取 
|DataWarmingLastRunOn|Datetime|在叢集中執行暖資料的最後日期/時間
|MergesSuccessRate|Double|在前10分鐘內成功完成的合併作業百分比。
|NotHealthyReason|String|指定叢集狀況不良的原因 
|IsAttentionRequired|Boolean|如果叢集需要操作小組關注
|AttentionRequiredReason|String|指定叢集需要注意的原因
|ProductVersion|String|指定產品資訊 (分支、版本等等) 
|FailedIngestOperations|Int64|過去10分鐘內的失敗內嵌作業數目
|FailedMergeOperations|Int64|前1小時內失敗的合併作業數目
|MaxExtentsInSingleTable|Int64|資料表中的範圍數目上限 (TableWithMaxExtents) 
|TableWithMaxExtents|String|具有最大範圍數目 (MaxExtentsInSingleTable 的資料表) 
|WarmExtentSize|Double|熱快取中的範圍大小總計（以位元組為單位）
|NumberOfDatabases|Int32|叢集中的資料庫數目

## <a name="show-capacity"></a>。顯示容量

```kusto
.show capacity
```

針對每個資源的估計叢集容量，傳回計算的結果。
 
**結果**

|輸出參數 |類型 |描述 
|---|---|---
|資源 |String |資源的名稱 
|總計 |Int64 |可用的資源總量（類型為「資源」）。 例如，並行內嵌的數目
|已耗用 |Int64 |目前使用的「資源」類型資源數量
|剩餘 |Int64 |「資源」類型的剩餘資源數量
 
**範例**

|資源 |總計 |已耗用 |剩餘
|---|---|---|---
|內嵌 |576 |1 |575

## <a name="show-operations"></a>。顯示作業

此命令會傳回資料表，其中包含自選擇新的管理員節點之後的所有系統管理作業。

|語法選項 |描述|
|---|---| 
|`.show` `operations`              |傳回叢集中正在處理或處理的所有作業
|`.show``operations` *OperationId*|傳回特定識別碼的作業狀態
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ... ) |傳回特定識別碼的作業狀態

**結果**
 
|輸出參數 |類型 |描述
|---|---|---
|ID |String |作業識別碼
|作業 |String |管理命令別名
|NodeId |String |如果命令正在遠端執行，例如 DataIngestPull。 節點識別碼將包含正在執行之遠端節點的識別碼
|StartedOn |Datetime |作業開始時的日期/時間 (UTC)  
|LastUpdatedOn |Datetime |上次更新作業時的日期/時間 (UTC) 。 作業可以是作業中的步驟，或完成步驟
|持續時間 |Datetime |LastUpdateOn 與 StartedOn 之間的時間範圍
|州 |String |具有值 "InProgress"、"Completed" 或 "Failed" 的命令狀態
|狀態 |String |包含失敗作業錯誤的其他說明字串
 
**範例**
 
|識別碼 |作業 |節點識別碼 |開始時間 |上次更新時間 |持續時間 |州 |狀態 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08：47：01.0000000 |2015-01-06 08：47：01.0000000 |0001-01-01 00：00：00.0000000 |已完成 | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto.Azure.Svc_IN_1 |2015-01-06 08：47：02.0000000 |2015-01-06 08：48：19.0000000 |0001-01-01 00：01：17.0000000 |已完成 | 
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08：47：18.0000000 |2015-01-06 08：47：18.0000000 |0001-01-01 00：00：00.0000000 |已完成 |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08：41：01.0000000 |0001-01-01 00：00：00.0000000 |0001-01-01 00：00：00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14：57：41.0000000 |2015-01-10 14：57：41.0000000 |0001-01-01 00：00：00.0000000 |Failed |集合已修改。 列舉操作可能無法執行 |
