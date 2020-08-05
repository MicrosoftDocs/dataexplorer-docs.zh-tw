---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5187f89a939daf45c0a5826e483de52cb7784103
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83383858"
---
Azure 資料總管會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰以用於資料加密。 

客戶管理的金鑰必須儲存在[Azure Key Vault](/azure/key-vault/key-vault-overview)中。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault API 來產生金鑰。 Azure 資料總管叢集和金鑰保存庫必須位於相同的區域中，但它們可以位於不同的訂用帳戶中。 如需客戶管理之金鑰的詳細說明，請參閱[客戶管理的金鑰與 Azure Key Vault](/azure/storage/common/storage-service-encryption)。 

本文說明如何設定客戶管理的金鑰。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

若要使用 Azure 資料總管來設定客戶管理的金鑰，您必須[在金鑰保存庫上設定兩個屬性](/azure/key-vault/key-vault-ovw-soft-delete)：「虛**刪除**」和「不要**清除**」。 預設不會啟用這些屬性。 若要啟用這些屬性，請在[PowerShell](/azure/key-vault/key-vault-soft-delete-powershell)中執行虛**刪除**並**啟用清除保護**，或在新的或現有的金鑰保存庫上啟用[Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) 。 僅支援大小為2048的 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[Key Vault 金鑰](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)。

> [!NOTE]
> [領導人和](../follower.md)後向叢集不支援使用客戶管理的金鑰進行資料加密

## <a name="assign-an-identity-to-the-cluster"></a>將身分識別指派給叢集

若要為您的叢集啟用客戶管理的金鑰，請先將系統指派的受控識別指派給叢集。 您將使用此受控識別來授與叢集許可權以存取金鑰保存庫。 若要設定系統指派的受控識別，請參閱[受控](../managed-identities.md)識別。
