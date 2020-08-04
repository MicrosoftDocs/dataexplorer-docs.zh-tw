---
title: 管理 Azure 資料總管叢集中的語言擴充功能。
description: 使用語言延伸模組，在您的 Azure 資料總管 KQL 查詢中整合其他語言。
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 104b3e4db18334f33c54177da7b996bc679db2de
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515884"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>管理 Azure 資料總管叢集中的語言延伸模組（預覽）

語言擴充功能可讓您使用語言擴充外掛程式，將其他語言整合到您的 Azure 資料總管 KQL 查詢中。 當您使用相關的腳本執行使用者定義函式（UDF）時，腳本會取得表格式資料做為其輸入，而且應該會產生表格式輸出。 外掛程式的執行時間裝載于[沙箱](kusto/concepts/sandboxes.md)、隔離且安全的環境中，並在叢集的節點上執行。 在本文中，您會在 Azure 入口網站的 Azure 資料總管叢集中管理語言延伸模組外掛程式。

> [!NOTE]
> 目前支援的 Azure 資料總管語言延伸模組為 Python 和 R。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)。

## <a name="enable-language-extensions-on-your-cluster"></a>在您的叢集上啟用語言擴充功能

> [!WARNING]
> 啟用語言擴充功能之前，請先參閱[限制](#limitations)。

請執行下列步驟，以在您的叢集上啟用語言擴充功能：

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**開啟**] 以啟用語言擴充功能。
1. 選取 [儲存]  。
 
    ![語言擴充功能](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> 啟用語言擴充程式可能需要幾分鐘的時間。 在這段期間，您的叢集將會暫停。
 
## <a name="run-language-extension-integrated-queries"></a>執行語言擴充整合的查詢

* 瞭解如何[執行 Python 整合式 KQL 查詢](kusto/query/pythonplugin.md)。
* 瞭解如何[執行 R 整合式 KQL 查詢](kusto/query/rplugin.md)。 

## <a name="disable-language-extensions-on-your-cluster"></a>停用叢集上的語言延伸模組

> [!NOTE]
> 停用語言擴充功能可能需要幾分鐘的時間。

請執行下列步驟，以停用叢集上的語言擴充功能：

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [關閉] 以停用語言擴充**功能**。
1. 選取 [儲存]  。

    ![語言延伸模組關閉](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>限制

* 語言擴充功能不支援[磁片加密](cluster-disk-encryption.md)。 
* 即使在相關語言的範圍中沒有執行任何查詢，語言延伸模組執行時間沙箱也會配置磁碟空間。
如需更詳細的限制，請參閱[沙箱](kusto/concepts/sandboxes.md)。
