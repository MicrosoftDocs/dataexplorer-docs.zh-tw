---
title: 使用 Logic Apps 在 Azure 資料總管中自動執行 Kusto 查詢
description: 瞭解如何使用 Logic Apps 自動執行 Kusto 查詢和命令並加以排程。
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: a4f79fc367e2769dfe3bf51ed5ad035a7d233ea4
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902686"
---
# <a name="microsoft-logic-app-and-azure-data-explorer-preview"></a>Microsoft 邏輯應用程式和 Azure 資料總管 (Preview) 

Azure Kusto 邏輯應用程式連線程式可讓您使用 [Microsoft 邏輯應用程式](/azure/logic-apps/logic-apps-what-are-logic-apps) 連接器，自動執行 Kusto 的查詢和命令，做為已排程或觸發之工作的一部分。

邏輯應用程式和 Power Automate 是建立在相同的連接器上。 因此，適用于 Power Automate 的 [限制](../../flow.md#limitations)、 [動作](../../flow.md#flow-actions)、 [驗證](../../flow.md#authentication) 和 [使用範例](../../flow-usage.md) ，也適用于 Logic Apps，如 [Power Automate 檔頁面](../../flow.md)所述。

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>如何使用 Azure 資料總管建立邏輯應用程式

1. 開啟 [Microsoft Azure 入口網站](https://ms.portal.azure.com/)。 
1. 搜尋並選取 `logic app`。

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="在 Azure 入口網站中搜尋「邏輯應用程式」，Azure 資料總管" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. 選取 [+新增]  。

    ![新增邏輯應用程式](./Images/logicapps/logicapp-add.png)

1. 輸入表單的必要詳細資料：
    * 訂用帳戶
    * 資源群組
    * 邏輯應用程式名稱
    * 區域或整合服務環境
    * 位置
    * 開啟或關閉記錄分析
1. 選取 [檢閱 + 建立]。

    ![建立邏輯應用程式](./Images/logicapps/logicapp-create-new.png)

1. 建立邏輯應用程式時，請選取 [ **編輯** ]。

    ![編輯邏輯應用程式設計工具](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. 建立空白的邏輯應用程式。

    ![邏輯應用程式空白範本](./Images/logicapps/logicapp-blanktemplate.png "logicapp-blanktemplate")

1. 新增週期性動作，然後選取 [ **Azure Kusto** ]。

    ![邏輯應用程式 Kusto 流程連接器](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>下一步

* 若要深入瞭解如何設定迴圈動作，請參閱 [Power Automate 檔頁面](../../flow.md)
* 請參閱一些 [使用範例](../../flow-usage.md) 以瞭解設定邏輯應用程式動作的概念
