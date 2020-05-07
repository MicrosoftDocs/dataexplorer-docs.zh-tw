---
title: Azure 資料總管連接器對電源自動化的使用範例（預覽）
description: 瞭解 Azure 資料總管連接器的一些常見使用範例，以自動進行電源自動化。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 0ecf0124051b6c003e056263afb6a3c5aa9ddb81
ms.sourcegitcommit: 98eabf249b3f2cc7423dade0f386417fb8e36ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868720"
---
# <a name="usage-examples-for-azure-data-explorer-connector-to-power-automate-preview"></a>Azure 資料總管連接器對電源自動化的使用範例（預覽）

Azure 資料總管 flow 連接器可讓 Azure 資料總管使用[Microsoft 電源自動化](https://flow.microsoft.com/)的流程功能。 您可以在已排程或觸發的工作中，自動執行 Kusto 查詢和命令。 本文包含數個常見的 flow 連接器使用範例。

如需詳細資訊，請參閱[Azure 資料總管 flow 連接器（預覽）](flow.md)。

## <a name="flow-connector-and-your-sql-database"></a>Flow 連接器和您的 SQL 資料庫

使用 flow 連接器來查詢您的資料，並將它匯總在 SQL 資料庫中。

> [!Note]
> 僅針對少量的輸出資料使用 flow 連接器。 SQL 插入作業會針對每個資料列分別執行。 

![使用 flow 連接器查詢資料的螢幕擷取畫面](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> 在 [叢集**名稱**] 欄位中，輸入叢集 URL。

## <a name="push-data-to-a-microsoft-power-bi-dataset"></a>將資料推送至 Microsoft Power BI 資料集

您可以使用 flow 連接器搭配 Power BI 連接器，將資料從 Kusto 查詢推送至 Power BI 串流資料集。

1. 建立新的「**執行查詢」和「列出結果**」動作。
1. 選取 [**新增步驟**]。
1. 選取 [**新增動作**]，然後搜尋 Power BI。
1. 選取 [ **Power BI** > **將資料列加入資料集**。 

    ![Power BI 連接器的螢幕擷取畫面](./media/flow-usage/flow-powerbiconnector.png)

1. 輸入將推送資料的**工作區**、**資料集**和**資料表**。
1. 從 [動態內容] 對話方塊中，加入包含資料集架構和相關 Kusto 查詢**結果的承載**。

    ![Power BI 欄位的螢幕擷取畫面](./media/flow-usage/flow-powerbifields.png)

此流程會針對 Kusto 查詢結果資料表的每個資料列自動套用 Power BI 動作。 

![每個資料列之 Power BI 動作的螢幕擷取畫面](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>條件式查詢

您可以使用 Kusto 查詢的結果做為下一個流程動作的輸入或條件。

在下列範例中，我們會查詢 Kusto 中是否有過去一天發生的事件。 針對每個已解決的事件，會張貼一則寬限訊息，並建立推播通知。
針對仍在作用中的每個事件，我們會查詢 Kusto 以取得類似事件的詳細資訊。 它會以電子郵件的形式傳送該資訊，並在 Azure DevOps Server 中開啟相關的工作。

請遵循這些指示來建立類似的流程：

1. 建立新的「**執行查詢」和「列出結果**」動作。
1. 選取 [**新增步驟** > **條件] 控制項**。
1. 從 [動態內容] 視窗中，選取您想要做為下一個動作之條件的參數。
1. 選取*關聯*性和*值*的類型，以設定特定參數的特定條件。

    [![](./media/flow-usage/flow-condition.png "Screenshot of flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    此流程會在查詢結果資料表的每個資料列上套用此條件。
1. 新增條件為 true 和 false 時的動作。

    [![](./media/flow-usage/flow-conditionactions.png "Screenshot of flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

您可以使用 Kusto 查詢的結果值做為下一個動作的輸入。 從 [動態內容] 視窗中選取結果值。
在下列範例中，我們新增了 [**時差]-[張貼訊息**] 動作和 [ **Visual Studio-建立新的工作專案**] 動作，其中包含 Kusto 查詢的資料。

![時差的螢幕擷取畫面-張貼訊息動作](./media/flow-usage/flow-slack.png)

![Visual Studio 動作的螢幕擷取畫面](./media/flow-usage/flow-visualstudio.png)

在此範例中，如果事件仍在作用中，重新查詢 Kusto 以取得來自相同來源的事件在過去的解決方式的相關資訊。

![流程條件查詢的螢幕擷取畫面](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> 在 [叢集**名稱**] 欄位中，輸入叢集 URL。

以圓形圖的形式將此資訊視覺化，並以電子郵件傳送給小組。

![流程條件電子郵件的螢幕擷取畫面](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>透過電子郵件傳送多個 Azure 資料總管流程圖

1. 使用「週期」觸發程式建立新流程，並定義流程的間隔和頻率。 
1. 新增一個步驟，其中包含一或多個**Kusto 執行查詢，並將結果動作視覺化**。 

    ![在流程中執行數個查詢的螢幕擷取畫面](./media/flow-usage/flow-severalqueries.png)

1. 針對每個**Kusto 執行查詢和視覺化結果**動作，定義下欄欄位：
    * 叢集 URL。
    * 資料庫名稱。
    * 查詢和圖表類型（例如，HTML 資料表、圓形圖、時間圖表、橫條圖或自訂值）。

    ![以多個附件視覺化結果的螢幕擷取畫面](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. 新增 **[傳送電子郵件（v2）** ] 動作： 
    1. 在 [內文] 區段中，選取 [程式碼視圖] 圖示。
    1. **在 [內**文] 欄位中，插入必要的**BodyHtml** ，讓查詢的視覺化結果包含在電子郵件的本文中。
    1. 若要將附件新增至電子郵件，請新增**附件名稱**和**附件內容**。
    
    ![以電子郵件傳送多個附件的螢幕擷取畫面](./media/flow-usage/flow-email-multiple-attachments.png)

    如需建立電子郵件動作的詳細資訊，請參閱[電子郵件 Kusto 查詢結果](flow.md#email-kusto-query-results)。 

結果：

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Screenshot of results of multiple attachments, visualized as a pie chart and bar chart")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Screenshot of results of multiple attachments, visualized as a time chart")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>後續步驟

瞭解[Azure Kusto 邏輯應用程式連接器](kusto/tools/logicapps.md)，這是在排定或觸發的工作中，可自動執行 Kusto 查詢和命令的另一種方式。

