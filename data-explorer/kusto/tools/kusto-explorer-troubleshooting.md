---
title: 針對 Kusto 中常見的問題進行疑難排解
description: 瞭解安裝和執行 Kusto 的常見問題及其解決方案
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 365ce1e4c8e5dd2157a7975095b9e30dfd1e3722
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342700"
---
# <a name="troubleshooting"></a>疑難排解

本檔提供執行和使用 Kusto 的常見問題，並提供解決方案。 本檔也會說明[如何重設 Kusto。](#reset-kustoexplorer)

## <a name="kustoexplorer-fails-to-start"></a>Kusto 無法啟動

### <a name="kustoexplorer-shows-error-dialog-during-or-after-start-up"></a>Kusto 會在啟動期間或之後顯示錯誤對話方塊

#### <a name="symptom"></a>徵狀

在啟動時，Kusto 會顯示 `InvalidOperationException` 錯誤。

#### <a name="possible-solution"></a>可能的解決方法

這項錯誤可能會建議作業系統損毀，或遺失一些必要的模組。
若要檢查遺失或損毀的系統檔案，請遵循以下所述的步驟：   
[https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system](https://support.microsoft.com/help/929833/use-the-system-file-checker-tool-to-repair-missing-or-corrupted-system)

## <a name="kustoexplorer-always-downloads-even-when-there-are-no-updates"></a>Kusto 一律會下載，即使沒有更新也一樣

#### <a name="symptom"></a>徵狀

每次開啟 Kusto 時，系統都會提示您安裝新的版本。 Kusto 會下載整個封裝，而不會更新已安裝的版本。

#### <a name="possible-solution"></a>可能的解決方法

此徵兆可能是您本機 ClickOnce 存放區損毀的結果。 您可以在提高許可權的命令提示字元中執行下列命令，以清除本機 ClickOnce 存放區。

> [!IMPORTANT]
> 1. 如果有任何其他 ClickOnce 應用程式或的實例 `dfsvc.exe` ，請在執行此命令之前先將它們終止。
> 1. 只要您有權存取儲存在應用程式快捷方式中的原始安裝位置，所有 ClickOnce 應用程式就會在您下次執行時自動重新安裝。 不會刪除應用程式快捷方式。

```kusto
rd /q /s %userprofile%\appdata\local\apps\2.0
```

請嘗試從其中一個 [安裝鏡像](kusto-explorer.md#installing-kustoexplorer)再次安裝 Kusto。

### <a name="clickonce-error-cannot-start-application"></a>ClickOnce 錯誤：無法啟動應用程式

#### <a name="symptoms"></a>徵兆

程式無法啟動，並顯示下列其中一個錯誤： 
* `External component has thrown an exception`
* `Value does not fall within the expected range`
* `The application binding data format is invalid.` 
* `Exception from HRESULT: 0x800736B2`
* `The referenced assembly is not installed on your system. (Exception from HRESULT: 0x800736B3)`

您可以按一下下列錯誤對話方塊，以流覽錯誤詳細資料 `Details` ：

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

#### <a name="proposed-solution-steps"></a>建議的解決方案步驟

1. 使用 `Programs and Features` () 來卸載 Kusto `appwiz.cpl` 。

1. 請嘗試執行 `CleanOnlineAppCache` ，然後再次嘗試安裝 Kusto。 
    從提升許可權的命令提示字元： 
    
    ```kusto
    rundll32 %windir%\system32\dfshim.dll CleanOnlineAppCache
    ```

    從其中一個 [安裝鏡像](kusto-explorer.md#installing-kustoexplorer)再次安裝 Kusto。

1. 如果應用程式仍未啟動，請刪除本機 ClickOnce 存放區。 只要您有權存取儲存在應用程式快捷方式中的原始安裝位置，所有 ClickOnce 應用程式就會在您下次執行時自動重新安裝。 不會刪除應用程式快捷方式。

    從提升許可權的命令提示字元：

    ```kusto
    rd /q /s %userprofile%\appdata\local\apps\2.0
    ```

    從其中一個[安裝鏡像](kusto-explorer.md#installing-kustoexplorer)再次安裝 Kusto

1. 如果應用程式仍無法啟動：
    1. 移除暫時部署檔案。
    1. 重新命名 Kusto. Explorer 本機 AppData 資料夾。

        從提升許可權的命令提示字元：

        ```kusto
        rd /s/q %userprofile%\AppData\Local\Temp\Deployment
        ren %LOCALAPPDATA%\Kusto.Explorer Kusto.Explorer.bak
        ```

    1. 從其中一個[安裝鏡像](kusto-explorer.md#installing-kustoexplorer)再次安裝 Kusto

    1. 若要從 Kusto 還原您的連接，請從提升許可權的命令提示字元：

        ```kusto
        copy %LOCALAPPDATA%\Kusto.Explorer.bak\User*.xml %LOCALAPPDATA%\Kusto.Explorer
        ```

#### <a name="enabling-clickonce-verbose-logging"></a>啟用 ClickOnce 詳細資訊記錄

1. 如果應用程式仍無法啟動：
    1. 在下列情況下建立 LogVerbosityLevel 字串值1，以[啟用詳細資訊 ClickOnce 記錄](https://docs.microsoft.com/visualstudio/deployment/how-to-specify-verbose-log-files-for-clickonce-deployments)：

        ```kusto
        HKEY_CURRENT_USER\Software\Classes\Software\Microsoft\Windows\CurrentVersion\Deployment
        ```
    
    1. 重新重現。
    1. 傳送詳細資訊輸出至 KEBugReport@microsoft.com 。 

### <a name="clickonce-error-your-administrator-has-blocked-this-application-because-it-potentially-poses-a-security-risk-to-your-computer"></a>ClickOnce 錯誤：您的系統管理員已封鎖此應用程式，因為這可能會對您的電腦造成安全性風險

#### <a name="symptom"></a>徵狀 
應用程式安裝失敗，發生下列其中一個錯誤：
* `Your administrator has blocked this application because it potentially poses a security risk to your computer`.
* `Your security settings do not allow this application to be installed on your computer.`

#### <a name="solution"></a>解決方法

此徵兆可能是因為另一個應用程式正在覆寫預設的 ClickOnce 信任提示行為。 
1. 查看您的預設設定。
1. 將您的設定與您電腦上的實際設定進行比較。
1. 視需要重設您的設定，如 [這篇](/visualstudio/deployment/how-to-configure-the-clickonce-trust-prompt-behavior)操作說明文章所述。

### <a name="cleanup-application-data"></a>清除應用程式資料

有時候，當先前的疑難排解步驟無法讓 Kusto 啟動時，清除儲存在本機的資料可能會有説明。

您可以在這裡找到 Kusto 所儲存的資料： `C:\Users\[your username]\AppData\Local\Kusto.Explorer` 。

> [!NOTE]
> 清除資料會導致遺失開啟的索引標籤 (復原資料夾) 、儲存的連線 (連接資料夾) ，以及 (u 資料夾) 的應用程式設定。

## <a name="reset-kustoexplorer"></a>重設 Kusto

如有需要，您可以完全重設 Kusto。 下列程式說明如何漸進式重設 Kusto，直到它從電腦移除，而且必須從頭開始安裝。

1. 在 Windows 中，開啟 [ **變更] 或 [移除程式** ] (也稱為 [ **程式和功能** ]) 。
1. 選取以開頭的每個專案 `Kusto.Explorer` 。
1. 選取 [解除安裝]。

   如果此程式無法卸載應用程式 (ClickOnce 應用程式) 的已知問題，請參閱 [這篇文章以取得相關指示](https://stackoverflow.com/questions/10896223/how-do-i-completely-uninstall-a-clickonce-application-from-my-computer)。

1. 刪除資料夾 `%LOCALAPPDATA%\Kusto.Explorer` ，以移除所有的連接、記錄等等。

1. 刪除資料夾 `%APPDATA%\Kusto` ，以移除 Kusto. Explorer 權杖快取。 您必須重新驗證所有群集。

也可以還原至特定版本的 Kusto。 Explorer：

1. 執行 `appwiz.cpl`。
1. 選取 [ **Kusto** ]，然後選取 [ **卸載/變更**]。
1. 選取 **[將應用程式還原為先前的狀態**]。

## <a name="next-steps"></a>後續步驟

* 深入瞭解 [Kusto. Explorer 使用者介面](kusto-explorer.md#overview-of-the-user-interface)
* 瞭解如何 [從命令列執行 Kusto](kusto-explorer-using.md#kustoexplorer-command-line-arguments)
* 瞭解 [Kusto Query Language (KQL) ](../query/index.md)