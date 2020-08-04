---
title: 使用 Azure 中的磁片加密來保護您的叢集資料總管-Azure 入口網站
description: 本文說明如何使用 Azure 入口網站中的 Azure 資料總管中的磁片加密來保護您的叢集。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 4f0ed42727759dae54f75fcb33ba1f024c218a4a
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515921"
---
# <a name="secure-your-cluster-using-disk-encryption-in-azure-data-explorer---azure-portal"></a>使用 Azure 中的磁片加密來保護您的叢集資料總管-Azure 入口網站

[Azure 磁碟加密](/azure/security/azure-security-disk-encryption-overview)有助於保護和保護您的資料，以符合組織的安全性和合規性承諾。 它提供叢集虛擬機器的 OS 和資料磁片的磁片區加密。 它也會與[Azure Key Vault](/azure/key-vault/)整合，可讓我們控制及管理磁片加密金鑰和密碼，並確保 VM 磁片上的所有資料都已加密。 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>在 Azure 入口網站中啟用待用加密
  
您的叢集安全性設定可讓您在叢集上啟用磁片加密。 在您的叢集上啟用待用[加密](/azure/security/fundamentals/encryption-atrest)可為儲存的資料（待用）提供資料保護。 

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集資源。 在 [**設定**] 標題下，選取 [**安全性**]。 

    ![開啟待用加密](media/manage-cluster-security/security-encryption-at-rest.png)

1. 在 [**安全性**] 視窗中，針對 [**磁片加密**] 安全性設定選取 [**開啟**]。 

1. 選取 [儲存]  。
 
> [!NOTE]
> 選取 [**關閉**] 以在啟用加密後予以停用。

## <a name="azure-data-explorer-stores-data-within-a-region"></a>Azure 資料總管會將資料儲存在區域內

每個 Azure 資料總管叢集都會在單一區域中的專用資源上執行。 所有資料都會儲存在區域內。 

## <a name="next-steps"></a>後續步驟

[檢查叢集健康情況](check-cluster-health.md)
