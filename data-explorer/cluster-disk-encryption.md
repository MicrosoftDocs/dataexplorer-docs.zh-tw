---
title: 在 Azure 資料總管 Azure 入口網站中使用磁片加密來保護您的叢集
description: 本文說明如何使用 Azure 中的磁片加密在 Azure 入口網站內資料總管保護您的叢集。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/02/2020
ms.openlocfilehash: 2a925e87f1fbc57b0b65f66abb6085e1083b864a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872330"
---
# <a name="secure-your-cluster-using-disk-encryption-in-azure-data-explorer---azure-portal"></a>在 Azure 資料總管 Azure 入口網站中使用磁片加密來保護您的叢集

[Azure 磁碟加密](/azure/security/azure-security-disk-encryption-overview) 可協助保護您的資料，以符合組織的安全性和合規性承諾。 它會針對叢集虛擬機器的 OS 和資料磁片提供磁片區加密。 它也與 [Azure Key Vault](/azure/key-vault/)整合，可讓我們控制及管理磁片加密金鑰和秘密，並確保 VM 磁片上的所有資料都已加密。 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>在 Azure 入口網站中啟用靜態加密
  
您的叢集安全性設定可讓您在叢集上啟用磁片加密。 在您的叢集上啟用待用 [加密](/azure/security/fundamentals/encryption-atrest) ，可為儲存的資料提供資料保護， (待用) 。 

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集資源。 在 [ **設定** ] 標題下，選取 [ **安全性**]。 

    ![開啟靜態加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在 [**安全性**] 視窗中，針對 [**磁片加密**安全性] 設定選取 [**開啟**]。 

1. 選取 \[儲存\]。
 
> [!NOTE]
> 選取 [ **關閉** ]，在啟用加密之後停用加密。

## <a name="azure-data-explorer-stores-data-within-a-region"></a>Azure 資料總管將資料儲存在區域內

每個 Azure 資料總管叢集都會在單一區域中的專用資源上執行。 所有資料都會儲存在區域內。 

## <a name="next-steps"></a>後續步驟

[檢查叢集健康情況](check-cluster-health.md)
