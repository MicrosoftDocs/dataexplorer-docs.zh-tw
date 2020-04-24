---
title: 使用 Azure 資源管理員樣本在 Azure 資料資源管理員中設定客戶管理金鑰
description: 本文介紹如何使用 Azure 資源管理員範本在 Azure 資料資源管理器中配置客戶管理的數據密鑰加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 1af7404a20c7246b50f80385f666da886f0cc96b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496962"
---
# <a name="configure-customer-managed-keys-using-the-azure-resource-manager-template"></a>使用 Azure 資源管理員樣本設定客戶託管金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>使用客戶管理的金鑰設定加密

在本節中,使用 Azure 資源管理器範本配置客戶管理的密鑰。 默認情況下,Azure 資料資源管理員加密使用Microsoft管理的密鑰。 在此步驟中,將 Azure 資料資源管理器群集配置為使用客戶管理的密鑰,並指定要與群集關聯的鍵。

可以使用 Azure 門戶或使用 PowerShell 部署 Azure 資源管理器範本。

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

建立金鑰的新版本時,需要更新群集才能使用新版本。 首先,打電話`Get-AzKeyVaultKey`獲取最新版本的密鑰。 然後更新群集的密鑰保管庫屬性以使用密鑰的新版本,如[使用客戶管理的密鑰配置加密](#configure-encryption-with-customer-managed-keys)所示。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料資源管理器群集](security.md)
* [為 Azure 資料資源管理器叢集設定託管識別](managed-identities.md)
* 以靜態開啟加密來保護[Azure 資料資源管理員 - Azure 門戶中的叢集](manage-cluster-security.md)。
* [使用 C 設定客戶託管金鑰#](customer-managed-keys-csharp.md)

