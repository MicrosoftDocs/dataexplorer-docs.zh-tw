---
title: 控制或禁止庫索托 SDK 客戶端追蹤 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了在 Azure 數據資源管理器中控制或禁止庫sto SDK 客戶端跟蹤。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 65964d7e990cdb639bd5bfe319d11874ea3de15d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502732"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>控制或抑制 Kusto SDK 客戶端追蹤

Kusto 用戶端庫使用通用平台進行跟蹤。 此平台在建譯過程中使用大量追蹤`System.Diagnostics.TraceSource`來源 (),每個源都連接到預設的追蹤`System.Diagnostics.Trace.Listeners`偵聽 器集 () 。

其中一個含義是,如果應用程式具有與預設`System.Diagnostics.Trace`實例關聯的跟蹤偵聽器(例如,通過`app.config`其檔),則 Kusto 用戶端庫將釋放對這些偵聽器的跟蹤。

可以通過程式設計或通過配置檔抑制或控制此行為。

## <a name="suppress-tracing-programmatically"></a>以程式設計方式禁止追蹤

要以程式設計方式禁止從 Kusto 用戶端庫進行追蹤,請儘早在載入相關庫時呼叫以下代碼段:

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>使用設定檔禁止追蹤

要透過設定檔禁止從 Kusto 用戶端函式庫進行追蹤`Kusto.Cloud.Platform.dll.tweaks`,請修改`Kusto.Data`檔案 (包含在庫中),以便適當的「調整」現在讀取:

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

(請注意,要生效的調整,值中不需要減號`key`。

另一種方法是執行以下操作:

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>如何開啟 Kusto 用戶端庫追蹤

要啟用從 Kusto 用戶端庫中追蹤,請在應用程式的 app.config 檔中啟用 .NET 跟蹤。 例如,假設應用程式`MyApp.exe`正在使用 Kusto.Data 用戶端庫。 然後,將檔案`MyApp.exe.config`更改為以下內容將啟用 Kusto.資料追蹤應用程式下一次啟動時:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.diagnostics>
    <trace indentsize="4">
      <listeners>
        <add type="Kusto.Cloud.Platform.Utils.RollingCsvTraceListener2, Kusto.Cloud.Platform" name="RollingCsvTraceListener" initializeData="RollingLogs" />
        <remove name="Default" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
``` 

這將配置一個追蹤偵聽器,該偵聽器在位於進程目錄中稱為`RollingLogs`子目錄中的子目錄中寫入 CSV 檔。 (當然,任何。也可以使用 NET 相容的跟蹤偵聽器類。 

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>如何開啟 AAD 用戶端函式庫 (ADAL) 追蹤

開啟 Kusto 用戶端函式庫的追蹤後,AAD 用戶端庫發出的追蹤也是如此(Kusto 用戶端庫會自動設定 ADAL 追蹤)

