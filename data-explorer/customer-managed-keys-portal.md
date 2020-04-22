---
title: 使用 Azure 門戶設定客戶託管金鑰
description: 本文介紹如何在 Azure 資料資源管理員中配置客戶管理的數據金鑰加密。
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 13ad30e53ff38e627f44bac96010ae6d21902cee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497742"
---
# <a name="configure-customer-managed-keys-using-the-azure-portal"></a>使用 Azure 閘戶設定客戶管理的金鑰

> [!div class="op_single_selector"]
> * [入口網站](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 範本](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="enable-encryption-with-customer-managed-keys-in-the-azure-portal"></a>在 Azure 門戶使用客戶管理的金鑰進行加密

本文介紹如何使用 Azure 門戶啟用客戶管理密鑰加密。 默認情況下,Azure 資料資源管理員加密使用Microsoft管理的密鑰。 將 Azure 資料資源管理器群集配置為使用客戶管理的密鑰,並指定要與群集關聯的密鑰。

1. 在[Azure 門戶](https://portal.azure.com/)中,轉到[Azure 資料資源管理器群集](create-cluster-database-portal.md#create-a-cluster)資源。 
1. 在門戶的左側窗格中選擇 > **「設定加密**」。 **Settings**
1. 在 **「加密**」窗格中,為**客戶管理的密鑰**設置選擇 **「打開」**。
1. 按下 **「選擇鍵**」。

    ![設定客戶管理的金鑰](media/customer-managed-keys-portal/cmk-encryption-setting.png)

1. 在「**從 Azure 金鑰保管庫」 選擇金鑰**「 視窗中,從下拉清單」 選擇現有的**金鑰保管庫**。 如果選擇 **「創建新**」[以建立新的密鑰保管庫](/azure/key-vault/quick-create-portal#create-a-vault),則將路由到 **「創建金鑰保管庫」** 螢幕。

1. 選擇**鍵**。
1. 選擇**版本**。
1. 按一下 [選取]。 

    ![從 Azure 金鑰保存庫中選擇金鑰](media/customer-managed-keys-portal/cmk-key-vault.png)

1. 在「**加密**」窗格中,現在包含金鑰,選擇 **「儲存**」 。 當 CMK 建立成功時,您將在**通知**中看到一條成功消息。

    ![儲存客戶管理的金鑰](media/customer-managed-keys-portal/cmk-encryption-setting.png)

通過為 Azure 資料資源管理器群集啟用客戶管理的密鑰,如果群集不存在,則將創建系統分配的標識。 此外,您將向所選密鑰保管庫上的 Azure 資料資源管理器群集提供所需的 get、wrapKey 和取消扭曲密鑰許可權,並獲取密鑰保管庫屬性。 

> [!NOTE]
> 選擇 **「關閉**」 以在建立客戶管理的金鑰後將其刪除。

## <a name="next-steps"></a>後續步驟

* [在 Azure 中保護 Azure 資料資源管理器群集](security.md)
* 以靜態開啟加密來保護[Azure 資料資源管理員 - Azure 門戶中的叢集](manage-cluster-security.md)。
* [使用 Azure 資源管理員樣本設定客戶託管金鑰](customer-managed-keys-resource-manager.md)
* [使用 C 設定客戶託管金鑰#](customer-managed-keys-csharp.md)



