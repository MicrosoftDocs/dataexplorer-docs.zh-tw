---
title: Kusto 控制項或隱藏 SDK 用戶端追蹤-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中控制和隱藏 Kusto SDK 用戶端追蹤。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 10/23/2018
ms.openlocfilehash: 2224fe28c7f0088ac1a16cdee4d452e354ff0800
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324749"
---
# <a name="controlling-and-suppressing-kusto-sdk-client-side-tracing"></a>控制和隱藏 Kusto SDK 用戶端追蹤

Kusto 用戶端程式庫會使用通用平臺進行追蹤。 平臺會使用大量的追蹤來源 (`System.Diagnostics.TraceSource`) ，而且每個都連接到其結構內 () 的預設追蹤接聽項集合 `System.Diagnostics.Trace.Listeners` 。

如果應用程式具有與預設實例相關聯的追蹤接聽項 `System.Diagnostics.Trace` (例如，透過其檔案 `app.config`) ，Kusto 用戶端程式庫會將追蹤發出至這些接聽程式。

您可以透過程式設計方式或透過設定檔來隱藏或控制追蹤。

## <a name="suppress-tracing-programmatically"></a>以程式設計方式隱藏追蹤

若要以程式設計方式隱藏 Kusto 用戶端程式庫的追蹤，請在載入相關連結庫時叫用這段程式碼：

```csharp
Kusto.Cloud.Platform.Utils.TraceSourceManager.SetTraceVerbosityForAll(
    Kusto.Cloud.Platform.Utils.TraceVerbosity.Fatal
    );
```

## <a name="use-a-config-file-to-suppress-tracing"></a>使用設定檔隱藏追蹤 

若要透過設定檔隱藏 Kusto 用戶端程式庫的追蹤，請修改連結 `Kusto.Cloud.Platform.dll.tweaks` 庫) 隨附的檔案 (`Kusto.Data` 。

```xml
    //Overrides the default trace verbosity level
    <add key="Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel" value="0" />
```

> [!NOTE]
> 若要讓調整生效，請不要將值設為減號 `key`

替代方式是：

```csharp
Kusto.Cloud.Platform.Utils.Anchor.Tweaks.SetProgrammaticAppSwitch(
    "Kusto.Cloud.Platform.Utils.Tracing.OverrideTraceVerbosityLevel",
    "0"
    );
```

## <a name="enable-the-kusto-client-libraries-tracing"></a>啟用 Kusto 用戶端程式庫追蹤

若要啟用 Kusto 用戶端程式庫的追蹤，請在應用程式的 *app.config* 檔案中啟用 .net 追蹤。 例如，假設應用程式 `MyApp.exe` 使用 Kusto 資料用戶端程式庫。 將檔案 *MyApp.exe.config* 變更為包含下列各項，將可 `Kusto.Data` 在下次應用程式啟動時啟用追蹤。

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

程式碼會設定追蹤接聽程式，以寫入至名為 *RollingLogs* 的子目錄中的 CSV 檔案。 子目錄位於進程的目錄中。

> [!NOTE]
> 任何。您也可以使用 NET 相容的追蹤接聽程式類別

## <a name="enable-the-azure-ad-client-libraries-adal-tracing"></a>啟用 Azure AD 用戶端程式庫 (ADAL) 追蹤

啟用 Kusto 用戶端程式庫的追蹤之後，Azure AD 用戶端程式庫的追蹤。 Kusto 用戶端程式庫會自動設定 ADAL 追蹤。
