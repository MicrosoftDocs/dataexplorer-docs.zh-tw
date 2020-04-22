---
title: 在 Azure 中保護 Azure 資料資源管理器群集
description: 瞭解如何在 Azure 資料資源管理器中保護群集。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495441"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>在 Azure 中保護 Azure 資料資源管理器群集

本文介紹了 Azure 數據資源管理器中的安全性,以説明您保護雲中的數據和資源並滿足企業的安全需求。 確保群集安全非常重要。 保護群集包括一個或多個 Azure 功能,其中包括安全訪問和存儲。 本文提供了説明您確保群集安全的資訊。

## <a name="managed-identities-for-azure-resources"></a>適用於 Azure 資源的受控識別

構建雲應用程式時面臨的一個常見挑戰是代碼中的憑據管理,以便對雲端服務進行身份驗證。 保護好這些認證是相當重要的工作。 憑據不應存儲在開發人員工作站中或簽入原始程式碼管理。 Azure Key Vault 可安全地儲存認證、祕密和其他金鑰，但是您的程式碼必須向 Key Vault 進行驗證，才可擷取這些項目。

Azure 資源功能的 Azure 活動目錄 (Azure AD) 託管識別解決了此問題。 這項功能會在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要您程式碼中的任何認證。 有關此服務的詳細資訊,請參閱 Azure 資源概述頁的[託管識別](/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="data-encryption"></a>資料加密

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

[Azure 磁碟加密](/azure/security/azure-security-disk-encryption-overview)有助於保護數據,以滿足組織安全性和合規性承諾。 它為群集虛擬機器的作業系統和數據磁碟提供卷加密。 Azure 磁碟加密還與[Azure 密鑰保管庫](/azure/key-vault/)整合,這使我們能夠控制和管理磁碟加密金鑰和機密,並確保 VM 磁碟上的所有數據都加密。 

### <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

默認情況下,數據使用Microsoft管理的密鑰進行加密。 為了對加密金鑰進行其他控制,可以提供客戶管理的密鑰,用於數據加密。 您可以使用自己的金鑰在儲存等級管理資料的加密。 客戶管理的金鑰用於保護和控制對根加密密鑰的訪問,根加密密鑰用於加密和解密所有資料。 客戶管理的密鑰提供了創建、旋轉、禁用和撤銷存取控制的更大靈活性。 您還可以審核用於保護數據的加密密鑰。

使用 Azure 金鑰保管庫儲存客戶管理的密鑰。 您可以建立自己的密鑰並將其存儲在密鑰保管庫中,也可以使用 Azure 金鑰保管庫 API 產生金鑰。 Azure 資料資源管理器群集和 Azure 密鑰保管庫必須位於同一區域中,但它們可以位於不同的訂閱中。 有關 Azure 金鑰保管庫的詳細資訊,請參閱[什麼是 Azure 密鑰保管庫?](/azure/key-vault/key-vault-overview) 關於客戶管理的金鑰的詳細說明,請參考[Azure 金鑰保存式庫的客戶管理金鑰](/azure/storage/common/storage-service-encryption)。 使用[C#](/azure/data-explorer/customer-managed-keys-csharp)或[Azure 資源管理員樣本](/azure/data-explorer/customer-managed-keys-resource-manager)在 Azure 資料資源管理器群集中設定客戶管理金鑰

> [!Note]
> 客戶託管金鑰依賴於 Azure 資源的託管標識,這是 Azure 活動目錄 (Azure AD) 的一項功能。 要在 Azure 門戶中配置客戶託管金鑰,需要將**系統分配的**託管標識配置為群集,如為 Azure[資料資源管理器群集配置託管標識](/azure/data-explorer/managed-identities)中所述。

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>在 Azure 金鑰保存者儲存客戶管理的金鑰

要在群集上啟用客戶管理的密鑰,請使用 Azure 密鑰保管庫來儲存金鑰。 您必須在金鑰保管庫中同時啟用 **「軟刪除****」和「不清除」** 屬性。 密鑰保管庫必須位於與群集相同的訂閱中。 Azure 資料資源管理器使用 Azure 資源的託管標識對密鑰保管庫進行加密和解密操作的身份驗證。 託管標識不支援跨目錄方案。

#### <a name="rotate-customer-managed-keys"></a>旋轉客戶管理的金鑰

您可以根據合規性策略在 Azure 密鑰保管庫中輪換客戶管理的密鑰。 旋轉金鑰時,必須更新群集以使用新密鑰 URI。 旋轉金鑰不會觸發群集中數據的重新加密。 

#### <a name="revoke-access-to-customer-managed-keys"></a>復原對客戶管理金鑰的存取

要復原對客戶管理金鑰的存取許可權,請使用 PowerShell 或 Azure CLI。 有關詳細資訊,請參閱[Azure 金鑰保管庫 PowerShell](/powershell/module/az.keyvault/)或[Azure 金鑰保管庫 CLI](/cli/azure/keyvault)。 由於 Azure 資料資源管理器無法存取加密金鑰,因此撤銷存取將阻止存取群集儲存等級中的所有資料。

> [!Note]
> 當 Azure 資料資源管理員識別取消對客戶管理金鑰的訪問時,它將自動掛起群集以刪除任何緩存的數據。 返回對密鑰的訪問后,需要手動恢復群集。

## <a name="role-based-access-control"></a>角色型存取控制

使用[基於角色的存取控制 (RBAC),](/azure/role-based-access-control/overview)您可以隔離團隊中的職責,並僅授予群集使用者所需的存取許可權。 您只能允許某些操作,而不是向所有人授予群集上的無限制許可權。 可以使用[Azure CLI](/azure/role-based-access-control/role-assignments-cli)或[Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)設定[Azure 入口中](/azure/role-based-access-control/role-assignments-portal)的[資料庫的存取控制](/azure/data-explorer/manage-database-permissions)。

## <a name="next-steps"></a>後續步驟

* 以靜態開啟加密來保護[Azure 資料資源管理員 - 門戶中的叢集](manage-cluster-security.md)。
* [為 Azure 資料資源管理器叢集設定託管識別](managed-identities.md)
* [使用 Azure 資源管理員樣本設定客戶託管金鑰](customer-managed-keys-resource-manager.md)
* [使用 C 設定客戶託管金鑰#](customer-managed-keys-csharp.md)

