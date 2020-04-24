---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: de8a37b1b27dc1bff377c6212c0bfea0a13c7de2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496481"
---
## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 門戶清理資源

按照[清理資源](../create-cluster-database-portal.md#clean-up-resources)的步驟刪除 Azure 門戶中的資源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清除資源

如果雲外殼仍然打開,則無需複製/運行第一行(讀取主機)。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```