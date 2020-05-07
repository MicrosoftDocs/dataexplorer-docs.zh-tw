---
title: 控制或隱藏 Kusto SDK 用戶端追蹤-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中控制或隱藏 Kusto SDK 用戶端追蹤。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: cbda69063e3b1a20549dbadb4641fc9fd3f51f57
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862049"
---
# <a name="controlling-or-suppressing-kusto-sdk-client-side-tracing"></a>控制或隱藏 Kusto SDK 用戶端追蹤

Kusto 用戶端程式庫會使用常見的追蹤平臺。 平臺會使用大量的追蹤來源（`System.Diagnostics.TraceSource`），每一個都連接到其結構中的預設追蹤接聽`System.Diagnostics.Trace.Listeners`項（）集。

其中一個含意是，如果應用程式有與預設`System.Diagnostics.Trace`實例相關聯的追蹤接聽項（例如，透過其`app.config`檔案），則 Kusto 用戶端程式庫將會發出追蹤給這些接聽項。

此行為可透過程式設計方式或設定檔來隱藏或控制。

## <a name="suppress-tracing-programmatically"></a>以程式設計方式隱藏追蹤

若要以程式設計方式隱藏 Kusto 用戶端程式庫中的追蹤，請在載入相關的程式庫時，及早叫用下列程式碼片段：

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="suppressing-tracing-by-using-a-config-file"></a>使用設定檔隱藏追蹤

若要透過設定檔隱藏來自 Kusto 用戶端程式庫的追蹤，請`Kusto.Cloud.Platform.dll.tweaks`修改檔案（隨附于連結`Kusto.Data`庫），讓適當的「調整」立即讀取：

```xml
    <!-- Overrides the default trace verbosity level -->
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

（請注意，若要讓調整生效，則的值中不需要有負號） `key`。

另一個替代方式是執行下列動作：

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="how-to-enable-the-kusto-client-libraries-tracing"></a>如何啟用 Kusto 用戶端程式庫追蹤

若要啟用 Kusto 用戶端程式庫的追蹤，請在應用程式的 app.config 檔案中啟用 .NET 追蹤。 例如，假設應用程式`MyApp.exe`使用 Kusto 用戶端程式庫。 然後，將檔案`MyApp.exe.config`變更為包含下列內容，將會啟用 Kusto。下次應用程式啟動時，資料會進行追蹤：

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

這會設定追蹤接聽程式，以寫入至位於進程目錄中名`RollingLogs`為的子目錄中的 CSV 檔案。 （當然，任何。也可以使用 NET 相容的追蹤接聽程式類別）。

## <a name="how-to-enable-the-aad-client-libraries-adal-tracing"></a>如何啟用 AAD 用戶端程式庫（ADAL）追蹤

啟用 Kusto 用戶端程式庫的追蹤之後，AAD 用戶端程式庫所發出的追蹤（Kusto 用戶端程式庫會自動設定 ADAL 追蹤）
