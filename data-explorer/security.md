---
title: 在 Azure 中保護 Azure 資料總管叢集
description: 瞭解如何保護 Azure 中的叢集資料總管。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d8220055549c3bfa2892e7267521b08c0591f817
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370259"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>在 Azure 中保護 Azure 資料總管叢集

本文提供資料總管 Azure 中的安全性簡介，協助您保護雲端中的資料和資源，並滿足企業的安全性需求。 請務必保護叢集的安全。 保護您的叢集包括一或多個 Azure 功能，包括安全存取和儲存。 本文提供的資訊可協助您保護叢集的安全。

## <a name="managed-identities-for-azure-resources"></a>適用於 Azure 資源的受控識別

建立雲端應用程式時常見的挑戰是您程式碼中的認證管理，用於驗證雲端服務。 保護好這些認證是相當重要的工作。 認證不應儲存在開發人員工作站或簽入原始檔控制中。 Azure Key Vault 可安全地儲存認證、祕密和其他金鑰，但是您的程式碼必須向 Key Vault 進行驗證，才可擷取這些項目。

適用于 Azure 資源的 Azure Active Directory （Azure AD）受控識別功能可解決此問題。 這項功能會在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要您程式碼中的任何認證。 如需此服務的詳細資訊，請參閱[適用于 Azure 資源的受控](/azure/active-directory/managed-identities-azure-resources/overview)識別總覽頁面。

## <a name="data-encryption"></a>資料加密

### <a name="azure-disk-encryption"></a>Azure 磁碟加密

[Azure 磁碟加密](/azure/security/azure-security-disk-encryption-overview)有助於保護和保護您的資料，以符合組織的安全性和合規性承諾。 它會為叢集虛擬機器的 OS 和資料磁片提供磁片區加密。 Azure 磁碟加密也與[Azure Key Vault](/azure/key-vault/)整合，可讓我們控制及管理磁片加密金鑰和密碼，並確保 VM 磁片上的所有資料都已加密。 

### <a name="customer-managed-keys-with-azure-key-vault"></a>客戶管理的金鑰與 Azure Key Vault

根據預設，資料會使用 Microsoft 管理的金鑰進行加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰以用於資料加密。 您可以使用自己的金鑰來管理儲存體層級的資料加密。 客戶管理的金鑰是用來保護及控制根加密金鑰的存取權，用來加密和解密所有資料。 客戶管理的金鑰提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護資料的加密金鑰。

使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault API 來產生金鑰。 Azure 資料總管叢集和 Azure Key Vault 必須位於相同的區域，但它們可以位於不同的訂用帳戶中。 如需 Azure Key Vault 的詳細資訊，請參閱[什麼是 Azure Key Vault？](/azure/key-vault/key-vault-overview)。 如需客戶管理之金鑰的詳細說明，請參閱[客戶管理的金鑰與 Azure Key Vault](/azure/storage/common/storage-service-encryption)。 使用[c #](customer-managed-keys-csharp.md)或[Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)在 Azure 資料總管叢集中設定客戶管理的金鑰

> [!Note]
> 客戶管理的金鑰依賴 Azure 資源的受控識別，這是一項 Azure Active Directory （Azure AD）的功能。 若要在 Azure 入口網站中設定客戶管理的金鑰，您必須依照[設定 Azure 資料總管](managed-identities.md)叢集的受控識別中的詳細說明，為您的叢集設定**SystemAssigned**受控識別。

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>將客戶管理的金鑰儲存在 Azure Key Vault

若要在叢集上啟用客戶管理的金鑰，請使用 Azure Key Vault 來儲存您的金鑰。 您必須同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫的屬性。 金鑰保存庫必須位於與叢集相同的訂用帳戶中。 Azure 資料總管會使用 Azure 資源的受控識別來驗證金鑰保存庫，以進行加密和解密作業。 受控識別不支援跨目錄案例。

#### <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 當金鑰旋轉時，您必須更新叢集以使用新的金鑰 URI。 輪替金鑰並不會在叢集中觸發資料重新加密。 

#### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

若要撤銷對客戶管理的金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[Azure Key Vault PowerShell](/powershell/module/az.keyvault/)或[Azure Key Vault CLI](/cli/azure/keyvault)。 撤銷存取權會封鎖對叢集儲存體層級中所有資料的存取，因為 Azure 資料總管會導致無法存取加密金鑰。

> [!Note]
> 當 Azure 資料總管識別已撤銷客戶管理金鑰的存取權時，它會自動暫停叢集以刪除任何快取的資料。 一旦傳回金鑰的存取權，就必須手動繼續叢集。

## <a name="role-based-access-control"></a>角色型存取控制

使用[角色型存取控制（RBAC）](/azure/role-based-access-control/overview)，您可以將小組內的職責區隔，並僅授與叢集使用者所需的存取權。 您可以只允許特定動作，而不是在叢集上提供每個人不受限制的許可權。 您可以使用[Azure CLI](/azure/role-based-access-control/role-assignments-cli)或[Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)，在[Azure 入口網站](/azure/role-based-access-control/role-assignments-portal)中設定[資料庫的存取控制](manage-database-permissions.md)。

## <a name="next-steps"></a>後續步驟

* 藉由啟用待用加密，[在 Azure 資料總管入口網站中保護您](manage-cluster-security.md)的叢集。
* [設定 Azure 資料總管叢集的受控識別](managed-identities.md)
* [使用 Azure Resource Manager 範本設定客戶管理的金鑰](customer-managed-keys-resource-manager.md)
* [使用 C 設定客戶管理的金鑰#](customer-managed-keys-csharp.md)

