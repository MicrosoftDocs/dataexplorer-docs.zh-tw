---
title: 指令管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的命令管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5685833431529af22aa4d8778d121f5a4dec16d1
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744323"
---
# <a name="commands-management"></a>命令管理

## <a name="show-commands"></a>.顯示命令 

`.show``commands`命令返回具有已達到最終狀態的管理員命令的表。 這些命令可供查詢 30 天。

命令表有兩列,其中包含每個已完成命令的資源消耗詳細資訊:
* 總 Cpu:此命令使用的總 CPU 時鐘時間(使用者模式 + 內核模式)。
* 資源利用率:包含與該命令(包括 TotalCpu)相關的所有資源利用率資訊的物件。

正在追蹤的資源消耗包括數據更新以及與當前管理命令關聯的任何查詢。
目前,"命令"表(.ingest、.set、.append、.set 或替換、.set 或追加)僅涵蓋某些管理命令,並且將來將逐步添加更多命令。


* [資料庫管理員或資料庫監視器](../management/access-control/role-based-authorization.md)可以看到在其資料庫上調用的任何命令。
* 其他使用者只能看到他們調用的命令。

**語法**

`.show` `commands`
 
**範例**
 
|用戶端活動Id |CommandType |Text |資料庫 |啟動 |上次更新時間 |Duration |State |RootActivityId |User |FailureReason |Application |主體 |總Cpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |範圍合併   |.合併同步操作...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Completed  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD 應用程式 id_5ba8cec2-9a70-e92c98cad651  |   |庫斯托.Azure.DM.Svc |aadapp_5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |• 「掃描範圍統計」: • 「最小資料掃描時間」: null, "MaxData掃描時間":null *,"緩存統計":{記憶體":""錯過":2,"命中":20、"磁盘":"錯過次數":2,"命中":0=,"記憶體峰值":159620640,"總Cpu":"00:00:03.5781250" | 
|科。RunCommand;710e08ca-2cd3-4d2d-b7bd-2738d335aa50 |資料擷取 |.ing 到 MyTable 名稱 ...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |失敗 |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |已釋放套接字連接。   |Kusto.Explorer |aaduser_...    |00:00:00   |• "掃描範圍統計": • "最小數據掃描時間":null,"最大數據掃描時間":null"緩存統計資訊":"記憶體":"記憶體":{未命中":0、"磁碟":{0","命中次數":0,"命中次數":0="記憶體峰值":0,"總 Cpu":"總 Cpu":"00:00:00:00"} 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |擴充區重建 |.合併同步操作...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Completed  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD 應用程式 id_5ba8cec2-9a70-e92c98cad651  |   |庫斯托.Azure.DM.Svc |aadapp_5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |• "掃描範圍統計": • "最小數據掃描時間":null,"MaxData掃描時間":null",緩存統計資訊":{記憶體":"記憶體":{},"命中":1、"磁碟":{0、"命中":0、"記憶體峰值":88828560,"總 Cpu":"00:00:00:00.8906250"。 

**範例:從資源利用率列中提取特定資料**

訪問資源利用列中的一個屬性是通過調用 ResourcesUtilization.xxx(其中 xxx 是屬性名稱)來完成的。
請注意,資源利用是一個動態列,因此為了處理其值,應首先將其轉換為特定的數據類型(使用轉換函數,如:到龍、到距、到時間跨度等)。  

例如：

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|啟動 |記憶體峰值 |總Cpu |Text
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500  | .附加Server_Boots<\|讓啟動源表 = 會話啟動;...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750  | .設置或追加 Web 使用<\|資料庫("固化資料庫")。WebUsage_v2 | 總結。。。 | 專案。。。
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000  | .設置或追加 AtlasClusterEventStats(.)\|<Atlas_Temp( 日期時間(2017-09-28 12:13:28.7621737),日期時間(2017-09-28 12:14:8168492))

## <a name="investigating-performance-issues"></a>調查性能問題

`.show``commands`可用於調查性能問題,因為它們允許查看每個控制命令消耗的資源。
以下示例是此類調查的簡單和有用的查詢。

### <a name="querying-the-totalcpu-column"></a>查詢總 Cpu 欄

最後一天使用 CPU 前 10 個查詢:

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

其 TotalCpu 在過去 10 小時內通過的所有查詢都超過了 3 分鐘:

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

其 TotalCpu 超過 5 分鐘過去 24 小時內的所有查詢,按使用者和主體分組:

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

時間圖表: 平均 CPU vs 95 百分位數 vs 最大 CPU :

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="querying-the-memorypeak"></a>查詢記憶體峰值

最後一天具有最高記憶體峰值值的前 10 個查詢:

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

平均記憶體峰值的時間圖 vs 第 95 百分位數 vs 最大記憶體峰值:

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
