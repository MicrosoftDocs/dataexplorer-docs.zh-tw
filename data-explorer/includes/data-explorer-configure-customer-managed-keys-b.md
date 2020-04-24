---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496988"
---
## <a name="create-a-new-key-vault"></a>建立新金鑰保存庫

要使用 PowerShell 建立新金鑰保管庫,請致電[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 用於儲存 Azure 資料資源管理員加密的客戶管理金鑰的金鑰保管庫必須啟用兩個金鑰保護設置,**即"軟刪除****"和"不清除**"。 在下面的範例中,將括弧中的占位符值替換為您自己的值。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>設定金鑰保存庫存取原則

接下來,為金鑰保管庫配置訪問策略,以便群集具有訪問它的許可權。 在此步驟中,您將使用以前分配給群集的系統分配的託管標識。 要設定金鑰保存的金鑰,請呼叫[Set-AzKeyVault 存取原則](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來,在密鑰保管庫中創建一個新密鑰。 要建立新金鑰,請呼叫[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 將括弧中的占位符值替換為您自己的值,並使用前面示例中定義的變數。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
