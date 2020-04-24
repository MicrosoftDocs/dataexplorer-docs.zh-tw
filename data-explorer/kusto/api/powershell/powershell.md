---
title: 使用 PowerShell 中的 .NET 用戶端函式庫 - Azure 資料資源管理員 ( Azure) 的管理員 。微軟文件
description: 本文介紹在 Azure 資料資源管理器中使用 PowerShell 的 .NET 用戶端庫。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 635a23021a1a8c30347bfa27ecd65886b46a6fea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503208"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>使用 PowerShell 的 .NET 用戶端函式庫

Azure 資料資源管理器 .NET 用戶端庫可以通過 PowerShell 與任意(非 PowerShell) .NET 庫的內建集成由 PowerShell 腳本使用。

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>取得用 PowerShell 撰寫文稿的 .NET 用戶端庫

使用 PowerShell 開始使用 Azure 資料資源管理員 .NET 用戶端庫:

1. 下載[`Microsoft.Azure.Kusto.Tools`NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)。
2. 提取套件中"工具"目錄的內容(例如使用 7-zip)。
3. 從`[System.Reflection.Assembly]::LoadFrom("path")`電源 shell 調用以載入所需的庫。 
    - 命令`"path"`的參數應指示提取檔案的位置。
4. 載入所有從屬 .NET 程式集後,創建 Kusto 連接字串,實例化*查詢提供者*或*管理提供者*,並執行查詢或命令(如下[例](powershell.md#examples)所示)。

有關詳細資訊,請參閱[Azure 資料資源管理員客戶端庫](../netfx/about-kusto-data.md)。

## <a name="examples"></a>範例

### <a name="initialization"></a>初始化

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
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

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>範例:執行管理員命令

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

輸出是:
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>範例:執行查詢

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

輸出是:

|StartTime           |EndTime             |劇集Id |EventId |State          |EventType         |傷害直接 |傷害間接 |死亡直接 |死亡間接
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |格魯吉亞        |Thunderstorm Wind |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |密西西比    |Thunderstorm Wind |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |大西洋南部 |噴水        |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |佛羅里達        |龍捲風           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |佛羅里達        |大雨        |             0 |               0 |           0 |             0