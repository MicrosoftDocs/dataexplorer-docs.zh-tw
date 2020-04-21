---
title: 管理 Azure 資料資源管理器群集中的語言擴展。
description: 使用語言擴展將其他語言整合到 Azure 資料資源管理員 KQL 查詢中。
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 52855651daccfe3c9baf5bb059530bcf7bfb0f19
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81502526"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>管理 Azure 資料資源管理器叢集集中的語言延伸(預覽)

語言擴充功能允許您使用語言擴展外掛程式將其他語言整合到 Azure 資料資源管理器 KQL 查詢中。 使用相關文本運行使用者定義函數 (UDF) 時,文稿將獲取表格數據作為其輸入,並有望生成表格輸出。 外掛程式的運行時託管在[沙箱](kusto/concepts/sandboxes.md)中,一個隔離和安全的環境,在群集的節點上運行。 在本文中,您將在 Azure 門戶中管理 Azure 資料資源管理器群集中的語言擴展外掛程式。

> [!NOTE]
> 當前支援的 Azure 資料資源管理員語言擴展是 Python 和 R。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)。

## <a name="enable-language-extensions-on-your-cluster"></a>在叢集上啟用語言擴充

> [!WARNING]
> 請在啟用語言延伸之前查看[限制](#limitations)。

執行以下步驟以在叢集上啟用語言延伸:

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 **「設定」** 中,選擇 **「設定**」 。。 
1. 在 **「設定」** 窗格中,選擇 **「打開**」以啟用語言擴展。
1. 選取 [儲存]  。
 
    ![語言延伸](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> 啟用語言擴展過程可能需要幾分鐘時間。 在此期間,群集將掛起。
 
## <a name="run-language-extension-integrated-queries"></a>執行語言擴充整合查詢

* 瞭解如何執行[Python 整合 KQL 查詢](kusto/query/pythonplugin.md)。
* 瞭解如何執行[R 整合 KQL 查詢](kusto/query/rplugin.md)。 

## <a name="disable-language-extensions-on-your-cluster"></a>關閉叢集上的語言擴充

> [!NOTE]
> 禁用語言擴展可能需要幾分鐘時間。

執行以下步驟來關閉叢集上的語言延伸:

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 **「設定」** 中,選擇 **「設定**」 。。 
1. 在 **「設定」** 窗格中,選擇 **「關閉**」 以關閉語言擴展。
1. 選取 [儲存]  。

    ![關閉語言延伸](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>限制

* 語言延伸功能不支援[磁碟加密](manage-cluster-security.md)。 
* 語言擴展運行時沙箱分配磁碟空間,即使相關語言範圍內沒有查詢運行也是如此。
有關更詳細的限制,請參閱[沙箱](kusto/concepts/sandboxes.md)。
