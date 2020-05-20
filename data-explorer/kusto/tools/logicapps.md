---
title: 使用 Logic Apps 自動執行 Kusto 查詢
description: 瞭解如何使用 Logic Apps 自動執行 Kusto 查詢和命令並加以排程
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 8765635e0eea8c1d41640bc0393d39a0afa5f971
ms.sourcegitcommit: e66c5f4b833b4f6269bb7bfa5695519fcb11d9fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83630149"
---
# <a name="microsoft-logic-app-and-azure-data-explorer"></a>Microsoft 邏輯應用程式和 Azure 資料總管

Azure Kusto 邏輯應用程式連接器可讓您使用[Microsoft 邏輯應用程式](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)連接器，自動執行 Kusto 查詢和命令，做為已排程或觸發工作的一部分。

邏輯應用程式和流程是建立在相同的連接器上。 因此，適用于 Flow 的[限制](flow.md#limitations)、[動作](flow.md#azure-kusto-flow-actions)、[驗證](flow.md#authentication)和[使用範例](flow.md#azure-kusto-flow-actions)也適用于 Logic Apps，如[Flow 檔頁面](flow.md)所述。

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>如何使用 Azure 資料總管建立邏輯應用程式

1. 開啟 [ [Microsoft Azure 入口網站](https://ms.portal.azure.com/)]。 
1. 搜尋 `logic app` 並加以選取。

    [![](./Images/logicapps/logicapp-search.png "Search for logic app")](./Images/logicapps/logicapp-search.png#lightbox)

1. 選取 [+新增]  。

    ![新增邏輯應用程式](./Images/logicapps/logicapp-add.png)

1. 輸入表單的必要詳細資料：
    * 訂用帳戶
    * 資源群組
    * 邏輯應用程式名稱
    * 區域或整合服務環境
    * Location
    * 開啟或關閉記錄分析
1. 選取 [檢閱 + 建立]  。

    ![建立邏輯應用程式](./Images/logicapps/logicapp-create-new.png)

1. 建立邏輯應用程式時，請選取 [**編輯**]。

    ![編輯邏輯應用程式設計工具](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. 建立空白邏輯應用程式。

    ![邏輯應用程式空白範本](./Images/logicapps/logicapp-blanktemplate.png "logicapp-blanktemplate")

1. 新增迴圈動作並選取 [ **Azure Kusto**]。

    ![邏輯應用程式 Kusto 流程連接器](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解如何設定迴圈動作，請參閱[Flow 檔頁面](flow.md)
* 請參閱一些[使用範例](flow.md#azure-kusto-flow-actions)，以瞭解設定邏輯應用程式動作的想法
