---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: orspodek
ms.openlocfilehash: d55077b5d1938caf6df49d34e68ece7e32c56f85
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350057"
---
## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 入口網站清除資源

遵循[清除資源](../create-cluster-database-portal.md#clean-up-resources)中的步驟，以刪除 Azure 入口網站中的資源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清除資源

如果 Cloud Shell 仍開啟，您就不需要複製/執行第一行 (Read-Host)。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```