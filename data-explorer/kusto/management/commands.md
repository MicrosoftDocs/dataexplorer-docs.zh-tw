---
title: 命令管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的命令管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e6a31e2c79ae658cceecfdad0ce307e2522bb55b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011409"
---
# <a name="commands-management"></a>命令管理

## <a name="show-commands"></a>。顯示命令 

`.show commands`傳回資料表，其中包含已達到最終狀態的系統管理命令。 這些命令可用來查詢30天。

命令資料表有兩個數據行，其中包含每個已完成命令的資源耗用量詳細資料。

* TotalCpu-此命令所使用的總 CPU 時鐘時間（使用者模式 + 核心模式）。
* ResourceUtilization-包含與該命令相關的所有資源使用資訊，包括 TotalCpu。

追蹤的資源耗用量包括資料更新，以及與目前管理命令相關聯的任何查詢。
目前，命令資料表（ `.ingest` 、 `.set` 、 `.append` 、 `.set-or-replace` 、 `.set-or-append` ）只涵蓋部分管理命令。 逐漸地，命令資料表中會加入更多命令。

* [資料庫管理員或資料庫監視器](../management/access-control/role-based-authorization.md)可以看到在其資料庫上叫用的任何命令。
* 其他使用者只能看到其所叫用的命令。

**語法**

`.show` `commands`
 
**範例**
 
|ClientActivityId |CommandType |文字 |資料庫 |StartedOn |LastUpdatedOn |持續時間 |State |RootActivityId |使用者 |FailureReason |應用程式 |主體 |TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ExtentsMerge   |。合併非同步作業 .。。    |DB1    |2017-09-05 11：08：07.5738569    |2017-09-05 11：08：09.1051161    |00：00：01.5312592   |已完成  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD 應用程式識別碼 = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM .Svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00：00：03.5781250   |{"ScannedExtentsStatistics"： {"MinDataScannedTime"： null，"MaxDataScannedTime"： null}，"CacheStatistics"： {Memory "： {" 遺漏 "：2，" 命中 "： 20}，" Disk "： {" 遺漏 "：2，" 命中 "： 0}}，" MemoryPeak "：159620640，" TotalCpu "：" 00：00： 03.5781250 "} 
|KE.RunCommand710e08ca-2cd3-4d2d-b7bd-2738d335aa50    |DataIngestPull |。內嵌至 MyTableName .。。   |TestDB |2017-09-04 16：00：37.0915452    |2017-09-04 16：04：37.2834555    |00：04：00.1919103   |Failed |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |已處置通訊端連接。   |Kusto.Explorer |以 aaduser name> = .。。    |00:00:00   |{"ScannedExtentsStatistics"： {"MinDataScannedTime"： null，"MaxDataScannedTime"： null}，"CacheStatistics"： {"Memory"： {"遺漏"：0，命中 "： 0}，" Disk "： {" 遺漏 "：0，" 命中 "： 0}}，" MemoryPeak "：0，" TotalCpu "：" 00:00:00 "} 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ExtentsRebuild |。合併非同步作業 .。。    |DB2    |2017-09-18 13：29：38.5945531    |2017-09-18 13：29：39.9451163    |00：00：01.3505632   |已完成  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD 應用程式識別碼 = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM .Svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00：00：00.8906250   |{"ScannedExtentsStatistics"： {"MinDataScannedTime"： null，"MaxDataScannedTime"： null}，"CacheStatistics"： {Memory "： {" 遺漏 "：0，" 命中 "： 1}，" Disk "： {" 遺漏 "：0，" 命中 "： 0}}，" MemoryPeak "：88828560，" TotalCpu "：" 00：00： 00.8906250 "} 

**範例：從 ResourceUtilization 資料行中解壓縮特定資料**

存取 ResourceUtilization 資料行內的其中一個屬性，是藉由呼叫 ResourcesUtilization.xxx （其中 xxx 是屬性名稱）來完成。
> [!NOTE] 
> `ResourceUtilization`這是動態資料行。 若要使用其值，您應該先將它轉換成特定的資料類型。 使用轉換函式，例如 `tolong` 、 `toint` 、 `totimespan` 。  

例如，

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|StartedOn |MemoryPeak |TotalCpu |文字
|--|--|--|--
| 2017-09-28 12：11：27.8155381   | 800396032 | 00：00：04.5312500 |。附加 Server_Boots <\| 讓 bootStartsSourceTable = SessionStarts; .。。
| 2017-09-28 11：21：26.7304547   | 750063056 | 00：00：03.8218750 |。 set-或-append WebUsage <\| 資料庫（' CuratedDB '）。WebUsage_v2 | 總結 .。。 | 專案 .。。
| 2017-09-28 12：16：17.4762522   | 676289120 | 00：00：00.0625000 |。 set-或-append AtlasClusterEventStats with （...） <\| Atlas_Temp （datetime （2017-09-28 12：13：28.7621737），datetime （2017-09-28 12：14：28.8168492））

## <a name="investigating-performance-issues"></a>調查效能問題

`.show``commands`可以用來調查效能問題，因為它們會顯示每個控制命令所使用的資源。

下列範例是這類調查的簡單且有用的查詢。

### <a name="query-the-totalcpu-column"></a>查詢 TotalCpu 資料行

過去一天中的前10個 CPU 耗用查詢。

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

過去10小時內的所有查詢，其 TotalCpu 已通過3分鐘。

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

過去24小時內的所有查詢，其 TotalCpu 已通過5分鐘，並依使用者和主體分組。

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Timechart：平均 CPU 和第95個百分位數與最大 CPU。

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="query-the-memorypeak"></a>查詢 MemoryPeak

過去一天中具有最高 MemoryPeak 值的前10名查詢。

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

平均 MemoryPeak 和第95個百分位數的 Timechart 和 Max MemoryPeak。

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
