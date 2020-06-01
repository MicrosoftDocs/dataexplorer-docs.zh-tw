---
title: 使用 Azure Resource Manager 範本在 Azure 資料總管中設定客戶管理的金鑰
description: 本文說明如何使用 Azure Resource Manager 範本，在 Azure 資料總管中的資料上設定客戶管理的金鑰加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1d7611537a96cf38ab33ddba30c83f721c90d11c
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257972"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本設定客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

在本節中，您會使用 Azure Resource Manager 範本來設定客戶管理的金鑰。 根據預設，Azure 資料總管加密會使用 Microsoft 管理的金鑰。 在此步驟中，請將您的 Azure 資料總管叢集設定為使用客戶管理的金鑰，並指定要與叢集產生關聯的金鑰。

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

當您建立新版本的金鑰時，您必須更新叢集以使用新的版本。 首先，呼叫 `Get-AzKeyVaultKey` 以取得金鑰的最新版本。 然後，將叢集的 key vault 屬性更新為使用新版本的金鑰，如以[客戶管理的金鑰設定加密](#configure-encryption-with-customer-managed-keys)中所示。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料總管叢集](security.md)
* [設定 Azure 資料總管叢集的受控識別](managed-identities.md)
* 藉由啟用待用加密，[在 Azure 資料總管 Azure 入口網站中保護您](manage-cluster-security.md)的叢集。
* [使用 C 設定客戶管理的金鑰#](customer-managed-keys-csharp.md)

