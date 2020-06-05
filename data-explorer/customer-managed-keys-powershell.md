---
title: 使用 PowerShell 設定客戶管理的金鑰
description: 本文說明如何使用 PowerShell 在 Azure 資料總管中的資料上設定客戶管理的金鑰加密。
author: orspod
ms.author: orspodek
ms.reviewer: roshauli
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: ff9ad23e1e534e831e981e64cb2839f5e36d67c7
ms.sourcegitcommit: a7e040fc844098323aa1c00e254bcbcd41fe587f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2020
ms.locfileid: "84428358"
---
# <a name="configure-customer-managed-keys-using-powershell"></a>使用 PowerShell 設定客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-powershell"></a>使用 PowerShell 搭配客戶管理的金鑰來啟用加密

本文說明如何使用 PowerShell 啟用客戶管理的金鑰加密。 根據預設，Azure 資料總管加密會使用 Microsoft 管理的金鑰。 將您的 Azure 資料總管叢集設定為使用客戶管理的金鑰，並指定要與叢集產生關聯的金鑰。

1. 執行下列命令以登入 Azure：

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 設定您的叢集註冊所在的訂用帳戶。

    ```azurepowershell-interactive
    Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```

1. 執行下列命令來設定新的金鑰。

    ```azurepowershell-interactive
    Update-AzKustoCluster -ResourceGroupName "mytestrg" -Name "mytestcluster" -KeyVaultPropertyKeyName "<key-name>" -KeyVaultPropertyKeyVaultUri "<key-vault-uri>" -KeyVaultPropertyKeyVersion "<key-version>"
    ```

1. 執行下列命令，並檢查 ' KeyVaultProperty ... '驗證已成功更新叢集的屬性。

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name "mytestcluster" -ResourceGroupName "mytestrg" | Format-List
    ```
