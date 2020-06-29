---
title: 針對 Kusto 中的常見問題進行疑難排解
description: 瞭解安裝和執行 Kusto 的常見問題及其解決方案
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 19ffbe2f5e14a2c003c24d145b713970e6bb31b8
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517761"
---
# <a name="troubleshooting"></a>疑難排解

本檔提供執行和使用 Kusto 的常見問題，並提供解決方案。 本檔也會說明[如何重設 Kusto](#reset-kustoexplorer)。

## <a name="kustoexplorer-fails-to-start"></a>Kusto 無法啟動

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto 會在啟動期間或之後顯示錯誤對話方塊

**徵兆**

在啟動時，Kusto 會顯示 `InvalidOperationException` 錯誤。

**可能的解決方案**

此錯誤可能會建議作業系統損毀或遺失一些必要的模組。
若要檢查遺失或損毀的系統檔案，請遵循這裡所述的步驟：   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto 一律會下載，即使沒有更新也一樣

**徵兆**

每次開啟 Kusto 時，系統會提示您安裝新的版本。 Kusto 會下載整個封裝，而不會更新已安裝的版本。

**可能的解決方案**

此徵兆可能是您本機 ClickOnce 存放區損毀的結果。 您可以在提升許可權的命令提示字元中執行下列命令，以清除本機 ClickOnce 存放區。

> [!Important]
> 1. 如果有任何其他 ClickOnce 應用程式或的實例 `dfsvc.exe` ，請先將其終止，然後再執行此命令。
> 1. 所有 ClickOnce 應用程式會在您下次執行時自動重新安裝，只要您可以存取儲存在應用程式快捷方式中的原始安裝位置即可。 應用程式快捷方式不會被刪除。

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

請嘗試從其中一個[安裝鏡像](kusto-explorer.md#installing-kustoexplorer)重新安裝 Kusto。

### <a name="clickonce-error-cannot-start-application"></a>ClickOnce 錯誤：無法啟動應用程式

**徵兆**  

程式無法啟動，並顯示下列其中一個錯誤： 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

您可以按一下下列錯誤對話方塊來流覽錯誤詳細資料 `Details` ：

![ClickOnce 錯誤](./Images/kusto-explorer-troubleshooting/clickonce-err-1.png)

```kusto
Following errors were detected during this operation.
    * System.ArgumentException
        - Value does not fall within the expected range.
        - Source: System.Deployment
        - Stack trace:
            at System.Deployment.Application.NativeMethods.CorLaunchApplication(UInt32 hostType, String applicationFullName, Int32 manifestPathsCount, String[] manifestPaths, Int32 activationDataCount, String[] activationData, PROCESS_INFORMATION processInformation)
            at System.Deployment.Application.ComponentStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.SubscriptionStore.ActivateApplication(DefinitionAppId appId, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.Activate(DefinitionAppId appId, AssemblyManifest appManifest, String activationParameter, Boolean useActivationParameter)
            at System.Deployment.Application.ApplicationActivator.ProcessOrFollowShortcut(String shortcutFile, String& errorPageUrl, TempFile& deployFile)
            at System.Deployment.Application.ApplicationActivator.PerformDeploymentActivation(Uri activationUri, Boolean isShortcut, String textualSubId, String deploymentProviderUrlFromExtension, BrowserSettings browserSettings, String& errorPageUrl)
            at System.Deployment.Application.ApplicationActivator.ActivateDeploymentWorker(Object state)
```

**建議的解決方案步驟**

1. 使用 `Programs and Features` （）卸載 Kusto。 `appwiz.cpl`

1. 請試著執行 `CleanOnlineAppCache` ，然後再次嘗試安裝 Kusto。 
    從提高許可權的命令提示字元： 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    再次從其中一個[安裝鏡像](kusto-explorer.md#installing-kustoexplorer)安裝 Kusto。

1. 如果應用程式仍未啟動，請刪除本機 ClickOnce 存放區。 所有 ClickOnce 應用程式會在您下次執行時自動重新安裝，只要您可以存取儲存在應用程式快捷方式中的原始安裝位置即可。 應用程式快捷方式不會被刪除。

    從提高許可權的命令提示字元：

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    從其中一個[安裝鏡像](kusto-explorer.md#installing-kustoexplorer)重新安裝 Kusto

1. 如果應用程式仍無法啟動：
    1. 移除暫存的部署檔案。
    1. 重新命名 Kusto. Explorer 本機 AppData 資料夾。

        從提高許可權的命令提示字元：

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. 從其中一個[安裝鏡像](kusto-explorer.md#installing-kustoexplorer)重新安裝 Kusto

    1. 從已提升許可權的命令提示字元，從 Kusto 還原您的連接：

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

1. 如果應用程式仍無法啟動：
    1. 在下方建立 LogVerbosityLevel 字串值1，以啟用詳細的 ClickOnce 記錄：

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. 再次重現。
    1. 將詳細資訊輸出傳送至 KEBugReport@microsoft.com 。 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce 錯誤：您的系統管理員已封鎖此應用程式，因為它可能會對您的電腦造成安全性風險

**徵兆**  
應用程式安裝失敗，發生下列其中一個錯誤：
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

**方案**

這個徵兆可能是因為另一個應用程式正在覆寫預設的 ClickOnce 信任提示行為。 
1. 查看您的預設設定。
1. 將您的設定值與電腦上的實際值進行比較。
1. 視需要重設您的設定，如[這篇操作說明文章中](https://docs.microsoft.com/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)所述。

### <a name="cleanup-application-data"></a>清除應用程式資料

有時候，當先前的疑難排解步驟無法協助 Kusto 啟動時，清除儲存在本機的資料可能會有説明。

Kusto 所儲存的資料可在這裡找到： `C:\Users\\[your alias]\AppData\Local\Kusto.Explorer` 。

> [!NOTE]
> 清除資料會導致開啟的索引標籤（復原資料夾）、儲存的連接（連接資料夾）和應用程式設定（UserSettings 資料夾）遺失。

## <a name="reset-kustoexplorer"></a>重設 Kusto

如有需要，您可以完全重設 Kusto。 下列程式描述如何漸進重設 Kusto，直到它完全從您的電腦移除，而且必須從頭開始安裝為止。

1. 在 Windows 中，開啟 [**變更] 或 [移除程式**] （也稱為 [**程式和功能**]）。
1. 選取每個以開頭的專案 `Kusto.Explorer` 。
1. 選取 [解除安裝]。

   如果此程式無法卸載應用程式（ClickOnce 應用程式的已知問題），請參閱[這篇文章以取得相關指示](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer)。

1. 刪除資料夾 `%LOCALAPPDATA%\Kusto.Explorer` ，這會移除所有連接、記錄等等。

1. 刪除資料夾 `%APPDATA%\Kusto` ，這會移除 Kusto 的權杖快取。 您必須重新驗證所有叢集。

也可以還原為特定版本的 Kusto。

1. 執行 `appwiz.cpl`。
1. 選取 [ **Kusto** ]，然後選取 [**卸載/變更**]。
1. 選取 **[將應用程式還原為先前的狀態**]。

## <a name="next-steps"></a>後續步驟

* 瞭解[Kusto. Explorer 使用者介面](kusto-explorer.md#overview-of-the-user-interface)
* 瞭解如何[從命令列執行 Kusto](kusto-explorer-using.md#kustoexplorer-command-line-arguments)
* 深入瞭解[Kusto 查詢語言（KQL）](https://docs.microsoft.com/azure/kusto/query/)
