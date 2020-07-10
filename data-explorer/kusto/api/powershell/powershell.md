---
title: 從 PowerShell Kusto .NET 用戶端程式庫-Azure 資料總管
description: 本文說明如何在 Azure 資料總管的 PowerShell 中使用 .NET 用戶端程式庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 6804b71ff3985de17460dddfa60f081f3bb910c0
ms.sourcegitcommit: b286703209f1b657ac3d81b01686940f58e5e145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86188416"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>從 PowerShell 使用 .NET 用戶端程式庫

PowerShell 腳本可以使用 Azure 資料總管 .NET 用戶端程式庫，透過 PowerShell 的內建整合與任意 (非 PowerShell) .NET 程式庫。

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>使用 PowerShell 取得 .NET 用戶端程式庫以進行腳本處理

使用 PowerShell 開始使用 Azure 資料總管 .NET 用戶端程式庫。

1. 下載[ `Microsoft.Azure.Kusto.Tools` NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)。
    * 如果您是使用 Powershell 7 (或更新版本) ，請下載[ `Microsoft.Azure.Kusto.Tools.NETCore` NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools.NETCore/)。
1. 將封裝中的「工具」目錄內容解壓縮 (使用) 之類的封存工具 `7-zip` 。
1. `[System.Reflection.Assembly]::LoadFrom("path")`從 PowerShell 呼叫以載入所需的程式庫。 
    * `path`命令的參數應該會指出解壓縮檔案的位置。
1. 載入所有相依的 .NET 元件之後：
   1. 建立 Kusto 連接字串。
   1. 具現化*查詢提供者*或系統*管理員提供者*。
   1. 執行查詢或命令，如下列[範例](powershell.md#examples)所示。

如需詳細資訊，請參閱[Azure 資料總管用戶端程式庫](../netfx/about-kusto-data.md)。

## <a name="examples"></a>範例

### <a name="initialization"></a>初始化

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example of the location from where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Part 2 of 3
#  ------------
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Part 3 of 3
#  ------------
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using Azure AD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using Azure AD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
#
#   NOTE: if you're running with Powershell 7 (or above) and the .NET Core library,
#         AAD user authentication with prompt will not work, and you should choose
#         a different authentication method.
```

### <a name="example-running-an-admin-command"></a>範例：執行管理命令

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

輸出如下：
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>範例：執行查詢

```powershell
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 5"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "StartTime" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort StartTime -Descending | Format-Table -AutoSize
```

輸出如下：

|StartTime           |EndTime             |EpisodeID |EventID |狀態          |EventType         |InjuriesDirect |InjuriesIndirect |DeathsDirect |DeathsIndirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |格魯吉亞        |Thunderstorm Wind |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |MISSISSIPPI    |Thunderstorm Wind |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |大西洋南部 |水 spout       |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |州        |龍捲風           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |州        |繁重 Rain        |             0 |               0 |           0 |             0
