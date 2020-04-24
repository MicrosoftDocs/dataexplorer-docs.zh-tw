---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496975"
---
Azure 資料資源管理器在靜態存儲帳戶中加密所有數據。 默認情況下,數據使用Microsoft管理的密鑰進行加密。 為了對加密金鑰進行其他控制,可以提供客戶管理的密鑰,用於數據加密。 

客戶管理的金鑰必須儲存在[Azure 金鑰保存庫中](/azure/key-vault/key-vault-overview)。 您可以建立自己的密鑰並將其存儲在密鑰保管庫中,也可以使用 Azure 金鑰保管庫 API 產生金鑰。 Azure 資料資源管理器群集和密鑰保管庫必須位於同一區域中,但它們可以位於不同的訂閱中。 關於客戶管理的金鑰的詳細說明,請參考[Azure 金鑰保存式庫的客戶管理金鑰](/azure/storage/common/storage-service-encryption)。 

本文介紹如何配置客戶管理的密鑰。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

要使用 Azure 資料資源管理員設定客戶管理的金鑰,必須在[密鑰保管庫上設置兩個屬性](/azure/key-vault/key-vault-ovw-soft-delete):**軟刪除**和 **「不清除**」。 默認情況下,這些屬性未啟用。 要啟用這些屬性,請在新金鑰保存式庫或現有金鑰保管庫執行啟用[PowerShell](/azure/key-vault/key-vault-soft-delete-powershell)或[Azure CLI](/azure/key-vault/key-vault-soft-delete-cli)中的**軟刪除**與**開啟清除保護**。 僅支援大小為 2048 的 RSA 密鑰。 關於金鑰的詳細資訊,請參考[金鑰保管庫金鑰](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)。

> [!NOTE]
> [領導者和跟隨者群集](/azure/data-explorer/follower)不支援使用客戶託管金鑰進行數據加密。 

## <a name="assign-an-identity-to-the-cluster"></a>向叢集分配識別

要為群集啟用客戶管理的密鑰,請先為群集分配系統分配的託管標識。 您將使用此託管標識授予訪問密鑰保管庫的群集許可權。 要設定系統分配的託管識別,請參閱[託管識別](/azure/data-explorer/managed-identities)。