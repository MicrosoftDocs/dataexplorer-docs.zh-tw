---
title: 使用 Azure Resource Manager 範本，在 Azure 資料總管中設定客戶管理的金鑰
description: 本文說明如何使用 Azure Resource Manager 範本，在 Azure 資料總管中的資料上設定客戶管理的金鑰加密。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: d67705722fb3f78cc4e69b2dcb38153ab3800b0b
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872024"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本設定客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

在本節中，您會使用 Azure Resource Manager 範本來設定客戶管理的金鑰。 根據預設，Azure 資料總管加密會使用 Microsoft 管理的金鑰。 在此步驟中，請將您的 Azure 資料總管叢集設定為使用客戶管理的金鑰，並指定要與叢集相關聯的金鑰。

您可以使用 Azure 入口網站或使用 PowerShell 來部署 Azure Resource Manager 範本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "defaultValue": "[concat('kusto', uniqueString(resourceGroup().id))]",
      "metadata": {
        "description": "Name of the cluster to create"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.Kusto/clusters",
      "sku": {
        "name": "Standard_D13_v2",
        "tier": "Standard",
        "capacity": 2
      },
      "apiVersion": "2019-09-07",
      "location": "[parameters('location')]",
      "properties": {
        "keyVaultProperties": {
          "keyVaultUri": "<keyVaultUri>",
          "keyName": "<keyName>",
          "keyVersion": "<keyVersion"
        }
      }
    }
  ]
}
```

## <a name="update-the-key-version"></a>更新金鑰版本

當您建立新版本的金鑰時，您必須將叢集更新為使用新版本。 首先，呼叫 `Get-AzKeyVaultKey` 以取得最新版本的金鑰。 然後，將叢集的金鑰保存庫內容更新為使用新版本的金鑰，如使用 [客戶管理的金鑰進行加密設定](#configure-encryption-with-customer-managed-keys)所示。

## <a name="next-steps"></a>後續步驟

* [保護 Azure 中的 Azure 資料總管叢集](security.md)
* [設定 Azure 資料總管叢集的受控識別](managed-identities.md)
* [在 Azure 中使用磁片加密來保護您的叢集資料總管 Azure 入口網站](cluster-disk-encryption.md) ，方法是啟用待用加密。
* [使用 C 設定客戶管理的金鑰#](customer-managed-keys-csharp.md)

