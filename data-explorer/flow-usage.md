---
title: '適用于 Azure 資料總管 connector 以 Power Automate (Preview 的使用範例) '
description: 瞭解 Azure 資料總管連接器 Power Automate 的一些常見使用範例。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 56851a159f6d8d2cee5f3991dab290070fb8c482
ms.sourcegitcommit: bc09599c282b20b5be8f056c85188c35b66a52e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88610486"
---
# <a name="usage-examples-for-azure-data-explorer-connector-to-power-automate-preview"></a>適用于 Azure 資料總管 connector 以 Power Automate (Preview 的使用範例) 

Azure 資料總管 flow 連接器可讓 Azure 資料總管使用 [Microsoft Power Automate](https://flow.microsoft.com/)的流程功能。 您可以在已排程或觸發的工作中自動執行 Kusto 查詢和命令。 本文包含數個常見的流程連接器使用範例。

如需詳細資訊，請參閱 [Azure 資料總管 flow 連接器 (Preview) ](flow.md)。

## <a name="flow-connector-and-your-sql-database"></a>Flow 連接器和您的 SQL database

使用 flow 連接器來查詢您的資料，並將其匯總在 SQL 資料庫中。

> [!Note]
> 只針對少量的輸出資料使用 flow 連接器。 針對每個資料列，SQL 插入作業會分開執行。 

![使用 flow 連接器來查詢資料的螢幕擷取畫面](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> 在 [叢集 **名稱** ] 欄位中，輸入叢集 URL。

## <a name="push-data-to-a-microsoft-power-bi-dataset"></a>將資料推送至 Microsoft Power BI 資料集

您可以使用 flow 連接器搭配 Power BI 連接器，將資料從 Kusto 查詢推送至 Power BI 的串流資料集。

1. 建立新的 **執行查詢並列出結果** 動作。
1. 選取 [新增步驟]。
1. 選取 [ **新增動作**]，然後搜尋 Power BI。
1. 選取**Power BI**  >  **將資料列加入至資料集**。 

    ![Power BI 連接器的螢幕擷取畫面](./media/flow-usage/flow-powerbiconnector.png)

1. 輸入將推送資料的 **工作區**、 **資料集**和 **資料表** 。
1. 從 [動態內容] 對話方塊中，加入包含資料集架構和相關 Kusto 查詢 **結果的承載** 。

    ![Power BI 欄位的螢幕擷取畫面](./media/flow-usage/flow-powerbifields.png)

流程會針對 Kusto 查詢結果資料表的每個資料列，自動套用 Power BI 動作。 

![每個資料列之 Power BI 動作的螢幕擷取畫面](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>條件式查詢

您可以使用 Kusto 查詢的結果做為下一個流程動作的輸入或條件。

在下列範例中，我們會查詢 Kusto，找出過去一天發生的事件。 針對每個已解決的事件，都會張貼一則延後訊息，並建立推播通知。
針對仍在使用中的每個事件，我們會查詢 Kusto 以取得類似事件的詳細資訊。 它會以電子郵件形式傳送該資訊，並在 Azure DevOps Server 中開啟相關工作。

請遵循下列指示來建立類似的流程：

1. 建立新的 **執行查詢並列出結果** 動作。
1. 選取 [**新增步驟**  >  **條件控制項**]。
1. 從動態內容視窗中，選取您要用來作為下一個動作條件的參數。
1. 選取 *關聯* 性和 *值* 的類型，以設定特定參數的特定條件。

    :::image type="content" source="./media/flow-usage/flow-condition.png" alt-text="使用以 Kusto 查詢結果為基礎的流程條件來判斷下一個流程動作，Azure 資料總管" lightbox="./media/flow-usage/flow-condition.png#lightbox":::

    此流程會將此條件套用至查詢結果資料表的每個資料列。
1. 在條件為 true 和 false 時新增動作。

    :::image type="content" source="./media/flow-usage/flow-conditionactions.png" alt-text="在條件為 true 或 false 時新增動作，以 Kusto 查詢結果為基礎的流程條件，Azure 資料總管" lightbox="./media/flow-usage/flow-conditionactions.png#lightbox":::

您可以使用 Kusto 查詢中的結果值作為下一個動作的輸入。 從動態內容視窗中選取結果值。
在下列範例中，我們會在 **訊息** 動作和 **Visual Studio 建立新的工作專案** 動作（包含 Kusto 查詢中的資料）。

![時差-張貼訊息動作的螢幕擷取畫面](./media/flow-usage/flow-slack.png)

![Visual Studio 動作的螢幕擷取畫面](./media/flow-usage/flow-visualstudio.png)

在此範例中，如果事件仍在使用中，請重新查詢 Kusto，以取得來自相同來源的事件在過去的解決方式的相關資訊。

![流程條件查詢的螢幕擷取畫面](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> 在 [叢集 **名稱** ] 欄位中，輸入叢集 URL。

將此資訊視覺化為圓形圖，然後以電子郵件傳送給小組。

![流程條件電子郵件的螢幕擷取畫面](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>傳送電子郵件給多個 Azure 資料總管 flow 圖表

1. 使用迴圈觸發程式建立新流程，並定義流程的間隔和頻率。 
1. 使用一或多個 **Kusto 執行查詢，並將結果動作視覺化** ，以新增步驟。 

    ![在流程中執行數個查詢的螢幕擷取畫面](./media/flow-usage/flow-severalqueries.png)

1. 針對每個 **Kusto 執行查詢，並以視覺化方式呈現結果** 動作，請定義下欄欄位：
    * 叢集 URL。
    * 資料庫名稱。
    * 查詢和圖表類型 (例如，HTML 資料表、圓形圖、時間圖表、橫條圖或自訂值) 。

    ![以多個附件視覺化結果的螢幕擷取畫面](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. 新增 **傳送電子郵件 (v2) ** 動作： 
    1. 在 [主體] 區段中，選取 [程式碼視圖] 圖示。
    1. 在 [內文 **] 欄位中** 插入必要的 **BodyHtml** ，讓查詢的視覺化結果包含在電子郵件的本文中。
    1. 若要將附件新增至電子郵件，請新增 **附件名稱** 和 **附件內容**。
    
    ![傳送多個附件的電子郵件螢幕擷取畫面](./media/flow-usage/flow-email-multiple-attachments.png)

    如需有關建立電子郵件動作的詳細資訊，請參閱 [電子郵件 Kusto 查詢結果](flow.md#email-kusto-query-results)。 

結果：

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments.png" alt-text="多個電子郵件附件的結果（視覺化為圓形圖和橫條圖），Azure 資料總管" lightbox="./media/flow-usage/flow-resultsmultipleattachments.png#lightbox":::

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments2.png" alt-text="多個電子郵件附件的結果（視覺化為時間圖表），Azure 資料總管" lightbox="./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox":::

## <a name="next-steps"></a>後續步驟

深入瞭解 [Azure Kusto 邏輯應用程式連線程式](kusto/tools/logicapps.md)，這是在排程或觸發的工作中，自動執行 Kusto 查詢和命令的另一種方式。

