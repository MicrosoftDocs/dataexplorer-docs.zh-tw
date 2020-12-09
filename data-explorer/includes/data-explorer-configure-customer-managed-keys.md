---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: c00ea9998b1ec6f05c36d0e0b926bd3b6f8d3509
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96933881"
---
Azure 資料總管會加密儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰，以用於資料加密。 

客戶管理的金鑰必須儲存在 [Azure Key Vault](/azure/key-vault/key-vault-overview)中。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，也可以使用 Azure Key Vault API 來產生金鑰。 Azure 資料總管叢集和金鑰保存庫必須位於相同的區域中，但它們可以在不同的訂用帳戶中。 如需客戶管理之金鑰的詳細說明，請參閱 [客戶管理的金鑰與 Azure Key Vault](/azure/storage/common/storage-service-encryption)。 

本文說明如何設定客戶管理的金鑰。

## <a name="configure-azure-key-vault"></a>設定 Azure Key Vault

若要使用 Azure 資料總管設定客戶管理的金鑰，您必須 [在金鑰保存庫上設定兩個屬性](/azure/key-vault/key-vault-ovw-soft-delete)：虛 **刪除** ，且 **不會清除**。 這些屬性預設不會啟用。 若要啟用這些屬性，請在 [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell)中執行虛 **刪除** 和 **啟用清除保護**，或在新的或現有的金鑰保存庫上 [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) 。 僅支援大小為2048的 RSA 金鑰。 如需有關金鑰的詳細資訊，請參閱 [Key Vault 金鑰](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)。

> [!NOTE]
> [領導人和](../follower.md)消費者叢集不支援使用客戶管理金鑰的資料加密

## <a name="assign-an-identity-to-the-cluster"></a>將身分識別指派給叢集

若要為您的叢集啟用客戶管理的金鑰，請先將系統指派或使用者指派的受控識別指派給叢集。 您將使用此受控識別來授與叢集存取金鑰保存庫的許可權。 若要設定受控識別，請參閱 [受控](../managed-identities.md)識別。
