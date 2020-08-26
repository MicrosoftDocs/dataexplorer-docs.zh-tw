---
title: 使用 Azure CLI 設定客戶管理的金鑰
description: 本文說明如何使用 Azure CLI 在 Azure 資料總管中的資料上設定客戶管理的金鑰加密。
author: orspod
ms.author: orspodek
ms.reviewer: astauben
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/01/2020
ms.openlocfilehash: c343ac1177e439f75116a3da77def5862d5d2a7f
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872126"
---
# <a name="configure-customer-managed-keys-using-azure-cli"></a>使用 Azure CLI 設定客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-using-azure-cli"></a>使用 Azure CLI 以客戶管理的金鑰啟用加密
本文說明如何使用 Azure CLI 用戶端來啟用客戶管理的金鑰加密。 根據預設，Azure 資料總管加密會使用 Microsoft 管理的金鑰。 設定您的 Azure 資料總管叢集以使用客戶管理的金鑰，並指定要與叢集相關聯的金鑰。

1. 執行下列命令以登入 Azure：

    ```azurecli-interactive
    az login
    ```

1. 設定您的叢集註冊所在的訂用帳戶。 將 *MyAzureSub* 取代為您想要使用的 Azure 訂用帳戶名稱。

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

1. 執行下列命令來設定新的金鑰。
    ```azurecli-interactive
    az kusto cluster update --cluster-name "mytestcluster" --resource-group "mytestrg" --key-vault-properties key-name="<key-name>" key-version="<key-version>" key-vault-uri="<key-vault-uri>"
    ```
1. 執行下列命令並檢查 ' keyVaultProperties ' 屬性，以確認已成功更新叢集。

    ```azurecli-interactive
    az kusto cluster show --cluster-name "mytestcluster" --resource-group "mytestrg"
    ```

