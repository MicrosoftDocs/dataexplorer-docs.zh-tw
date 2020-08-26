---
title: 使用 Azure 入口網站設定客戶管理的金鑰
description: 本文說明如何在 Azure 資料總管中為您的資料設定客戶管理的金鑰加密。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 4d828905ce673c3b1c6453d31974334312bd3b5e
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872143"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>使用 Azure 入口網站設定客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>在 Azure 入口網站中使用客戶管理的金鑰來啟用加密

本文說明如何使用 Azure 入口網站來啟用客戶管理的金鑰加密。 根據預設，Azure 資料總管加密會使用 Microsoft 管理的金鑰。 設定您的 Azure 資料總管叢集以使用客戶管理的金鑰，並指定要與叢集相關聯的金鑰。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，移至您的 [Azure 資料總管](create-cluster-database-portal.md#create-a-cluster) 叢集資源。 
1. **Settings**  >  在入口網站的左窗格中選取 [設定**加密**]。
1. 在 [**加密**] 窗格中，針對 [**客戶管理的金鑰**] 設定選取 [**開啟**]。
1. 按一下 [ **選取金鑰**]。

    ![設定客戶管理的金鑰](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. 在 [ **從 Azure Key Vault 選取金鑰** ] 視窗中，從下拉式清單中選取現有的 **金鑰保存庫** 。 如果您選取 [ **建立新** 的] 以 [建立新的 Key Vault](/azure/key-vault/quick-create-portal#create-a-vault)，則會將您路由傳送至 [ **建立] Key Vault** 畫面。

1. 選取 [ **金鑰**]。
1. 選取 [ **版本**]。
1. 按一下 [選取]  。

    ![從 Azure Key Vault 選取金鑰](media/customer-managed-keys-portal/cmk-key-vault.png)

1. 在現在包含金鑰的 [ **加密** ] 窗格中，選取 [ **儲存**]。 當 CMK 建立成功時，您會在 **通知**中看到成功訊息。

    ![儲存客戶管理的金鑰](media/customer-managed-keys-portal/cmk-encryption-setting.png)

藉由為您的 Azure 資料總管叢集啟用客戶管理的金鑰，您將會為叢集建立系統指派的身分識別（如果不存在的話）。 此外，您將會在所選 Key Vault 上提供所需的 get、wrapKey 和 unwarpKey 許可權給您的 Azure 資料總管叢集，並取得 Key Vault 屬性。 

> [!NOTE]
> 選取 [ **關閉** ]，即可在建立客戶管理的金鑰後將其移除。

## <a name="next-steps"></a>後續步驟

* [保護 Azure 中的 Azure 資料總管叢集](security.md)
* [在 Azure 中使用磁片加密來保護您的叢集資料總管 Azure 入口網站](cluster-disk-encryption.md) ，方法是啟用待用加密。
* [使用 Azure Resource Manager 範本設定客戶管理的金鑰](customer-managed-keys-resource-manager.md)
* [使用 C 設定客戶管理的金鑰#](customer-managed-keys-csharp.md)



