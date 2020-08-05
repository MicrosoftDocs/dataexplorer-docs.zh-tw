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
## <a name="create-a-new-key-vault"></a>建立新的金鑰保存庫

若要使用 PowerShell 建立新的金鑰保存庫，請呼叫[AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 您用來儲存 Azure 資料總管加密客戶管理金鑰的金鑰保存庫，必須啟用兩個金鑰保護設定： [虛**刪除**] 和 [**不要清除**]。 以您自己的值取代括弧中的預留位置值，如下列範例所示。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>設定 key vault 存取原則

接下來，設定金鑰保存庫的存取原則，讓叢集有許可權可以存取它。 在此步驟中，您將使用先前指派給叢集的系統指派受控識別。 若要設定金鑰保存庫的存取原則，請呼叫[set-set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，在金鑰保存庫中建立新的金鑰。 若要建立新的金鑰，請呼叫[AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 以您自己的值取代括弧中的預留位置值，並使用先前範例中所定義的變數。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
