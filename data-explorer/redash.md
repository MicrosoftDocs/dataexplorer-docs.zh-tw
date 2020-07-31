---
title: 使用 Redash 將 Azure 資料總管視覺化
description: 在本文中，您將瞭解如何使用 Redash native connector 將 Azure 資料總管中的資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 99d242358f5b4a54f7e1faa246a1cfc64d1414d3
ms.sourcegitcommit: 6e84f50efc8c5c3fe57080341ed3effe72197886
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439867"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>在 Redash 中將 Azure 資料總管的資料視覺化

[Redash](https://redash.io/)會連接和查詢您的資料來源、建立儀表板以將資料視覺化，並與對等共用。 在本文中，您將瞭解如何設定 Azure 資料總管做為 Redash 的資料來源，然後將資料視覺化。

## <a name="prerequisites"></a>必要條件

1. [建立叢集和資料庫](create-cluster-database-portal.md)。
1. 內嵌資料，如將[範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)中所述。 如需更多的內嵌選項，請參閱內嵌[總覽](ingest-data-overview.md)。

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>在 Redash 中建立 Azure 資料總管連接器 

1. 登入[Redash](https://redash.io/)。 選取 [**開始**使用] 來建立帳戶。
1. 在 [**現在就開始**吧] 底下，選取 **[連接資料來源]**。

    ![連線資料來源](media/redash/connect-data-source.png)

1. 在 [**建立新的資料來源**] 視窗中，選取 **[Azure 資料總管（Kusto）**]，然後選取 [**建立**]。 

    ![選取 Azure 資料總管資料來源](media/redash/select-adx-data-source.png)

1. 在 **[Azure 資料總管（Kusto）** ] 視窗中，完成下列表單，然後選取 [**建立**]。

    ![Azure 資料總管（Kusto） [設定] 視窗](media/redash/adx-settings-window.png)

1. 在 [**設定**] 視窗中，選取 [**儲存**並**測試**連線] 以測試您的**Azure 資料總管（Kusto）** 資料來源連接。

## <a name="create-queries-in-redash"></a>在 Redash 中建立查詢

1. 在 Redash 的左上方，選取 [**建立**  >  **查詢**]。 按一下 [追加**查詢**]，並將查詢重新命名。

    ![建立查詢](media/redash/create-query.png)

1. 在頂端的編輯窗格中輸入查詢，然後選取 [**儲存**並**執行**]。 選取 [**發行**] 以發行查詢以供未來使用。

    ![儲存並執行查詢](media/redash/save-and-execute-query.png)

    在左窗格中，您可以在下拉式功能表中看到資料來源連接名稱（我們的流程中的**Github 連接器**），以及所選資料庫中的資料表。 

1. 在下方的中央窗格中，查看查詢結果。 選取 [**新增視覺效果**] 按鈕，以建立視覺效果來進行查詢。

    ![新增視覺效果](media/redash/new-visualization.png)

1. 在 [視覺效果] 畫面中，選取 [**視覺效果類型**] 和相關欄位，例如 [ **X**資料行] 和 [ **Y 資料行**]。 **儲存**視覺效果。

    ![設定和儲存視覺效果](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>使用參數建立查詢

1. **建立**  > **查詢**以建立新的查詢。 使用 {} 大括弧將參數新增至其中 {} 。 選取 **{ {} }** 以開啟 [**新增參數**視窗]。 您也可以選取 [*設定] 圖示*來修改現有參數的屬性，並開啟 [ **<parameter_name>** ] 視窗。 

    ![插入參數](media/redash/insert-parameter.png)

1. 命名您的參數。 從下拉式功能表選取 [**類型**：以**查詢為基礎的下拉式清單]** 。 選取 [確定]

    ![以查詢為基礎的下拉式清單](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 查詢會使用多個值，因此您必須包含下列語法 `| where Type in ((split('{{Type}}', ',')))` 。 如需詳細資訊，請參閱[in 運算子](kusto/query/inoperator.md)。 這會導致[redash 應用程式中有多個查詢參數選項](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>在 Redash 中建立儀表板

1. 若要建立儀表板，請**建立**  >  **儀表板**。 或者，選取 [現有儀表板]、[**儀錶**板] > 從清單中選取儀表板。

    ![建立儀表板](media/redash/create-dashboard.png)

1. 在 [**新儀表板**] 視窗中，將儀表板命名為，然後選取 [**儲存**] 在 **<Dashboard_name>** ] 視窗中，選取 [新增**widget** ] 以建立新的 widget。 

1. 在 [**加入 Widget** ] 視窗中，選取 [查詢名稱]、**選擇 [視覺效果**] 和 [**參數**] 選取 [**新增至儀表板**]

   ![選擇視覺效果並新增至儀表板](media/redash/add-widget-window.png)

1. 選取 [**完成編輯**] 以完成建立儀表板。

1.  在儀表板編輯模式中，選取 [**使用儀表板層級篩選**]，以使用先前定義的**類型**參數。

    ![完成建立儀表板](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)


