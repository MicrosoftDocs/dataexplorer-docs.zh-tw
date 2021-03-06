---
title: 使用 Sisense 將 Azure 資料總管中的資料視覺化
description: 在本文中，您將瞭解如何將 Azure 資料總管設定為 Sisense 的資料來源，並將資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.custom: has-adal-ref
ms.date: 5/29/2019
ms.openlocfilehash: d7e476a6396d4ba695dd290226c3ace4d77153ba
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875390"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>在 Sisense 中將 Azure 資料總管中的資料視覺化

Sisense 是一種分析商務智慧平臺，可讓您建立可提供高度互動使用者體驗的分析應用程式。 商業智慧與儀表板報告軟體可讓您只需按幾下滑鼠，就能存取及合併資料。 您可以連接到結構化和非結構化資料來源、從多個來源聯結資料表，並提供基本的腳本和程式碼撰寫，以及建立互動式 web 儀表板和報表。 在本文中，您將瞭解如何將 Azure 資料總管設定為 Sisense 的資料來源，以及如何將範例叢集的資料視覺化。

## <a name="prerequisites"></a>先決條件

您需要下列專案才能完成這篇文章：

* [下載並安裝 Sisense 應用程式](https://documentation.sisense.com/latest/getting-started/download-install.htm)

* 建立包含 StormEvents 範例資料的叢集和資料庫。 如需詳細資訊，請參閱 [快速入門：建立 azure 資料總管叢集和資料庫](create-cluster-database-portal.md) ，並將 [範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>使用 Azure 資料總管 JDBC connector 連接至 Sisense 儀表板

1. 將下列 jar 檔案的最新版本下載並複製到中 *。\Sisense\DataConnectors\jdbcdrivers\adx*

    * activation-1.1 .jar
    * adal4j-1.6.0 .jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1 .jar
    * commons-lang3-3.5.jar
    * gson-2.8.0 .jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1 .jar
    * lang-tag-1.4.4 .jar
    * mail-1.4.7 .jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1 .jar
    * oauth2-oidc-sdk-5.24.1 .jar
    * slf4j-api-1.7.21 .jar

1. 開啟 **Sisense** 應用程式。
1. 選取 [ **資料** ] 索引標籤，然後選取 [ **+ ElastiCube** ] 以建立新的 ElastiCube 模型。

    ![選取 ElastiCube](media/sisense/data-select-elasticube.png)

1. 在 [ **加入新的 ElastiCube 模型**] 中，為 ElastiCube 模型命名並 **儲存**。

    ![加入新的 ElastiCube 模型](media/sisense/add-new-elasticube-model.png)

1. 選取 [ **+ 資料**]。

    ![選取資料按鈕](media/sisense/select-data.png)

1. 在 [ **選取連接器** ] 索引標籤中，選取 [ **一般 JDBC** 連接器]。

    ![選擇 JDBC 連接器](media/sisense/select-connector.png)

1. 在 [ **連接** ] 索引標籤中，完成 **一般 JDBC** 連接器的下欄欄位，然後選取 **[下一步]**。

    ![JDBC 連接器設定](media/sisense/jdbc-connector.png)

    |欄位 |描述 |
    |---------|---------|
    |連接字串     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC Jar 資料夾  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |驅動程式的類別名稱    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |使用者名稱   |    AAD 使用者名稱     |
    |密碼     |   AAD 使用者密碼      |

1. 在 [ **選取資料** ] 索引標籤中，搜尋 [ **選取資料庫** ]，選取您擁有許可權的相關資料庫。 在此範例中，請選取 *test1*。

    ![選取資料庫](media/sisense/select-database.png)

1. 在 [ *測試* (資料庫名稱]) 窗格中：
    1. 選取資料表名稱以預覽資料表，並查看資料表資料行名稱。 您可以移除不必要的資料行。
    1. 選取相關資料表的核取方塊，以選取該資料表。
    1. 選取 [完成]。

    ![選取資料表](media/sisense/select-table-see-columns.png)

1. 選取 [ **建立** ] 以建立您的資料集。

    * 在 [ **組建** ] 視窗中，選取 [ **組建**]。

      ![組建視窗](media/sisense/build-window.png)

    * 等候組建程式完成，然後選取 [ **建立成功**]。

      ![組建成功](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>建立 Sisense 儀表板

1. 在 [**分析**] 索引標籤中，選取 [ **+**  >  **新增儀表板**]，在此資料集上建立

    ![新增儀表板](media/sisense/new-dashboard.png)

1. 挑選儀表板，然後選取 [ **建立**]。

    ![建立儀表板](media/sisense/create-dashboard.png)

1. 在 [ **新增小工具**] 底下，選取 [ **+ 選取資料** ] 以建立新的 widget。

    ![將欄位新增至 StormEvents 儀表板](media/sisense/storm-dashboard-add-field.png)

1. 選取 [ **+ 新增更多資料** ]，將其他資料行新增至您的圖形。

    ![將更多資料新增至圖表](media/sisense/add-more-data.png)

1. 選取 [ **+ widget** ] 以建立另一個 widget。 拖放小工具以重新排列您的儀表板。

    ![Storm 儀表板](media/sisense/final-dashboard.png)

您現在可以使用視覺分析探索您的資料、建立額外的儀表板，以及將資料轉換成可採取行動的見解，以對您的業務造成影響。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)
