---
title: 使用 Sisense 視覺化 Azure 資料資源管理員中的資料
description: 在本文中,瞭解如何將 Azure 數據資源管理員設置為 Sisense 的數據源,並可視化數據。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 33a3e51e8bd892a5d4318445ff8e5f9ee26c4b40
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501187"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>在 Sisense 中視覺化來自 Azure 資料資源管理員的資料

Sisense 是一個分析商業智慧平臺,使您能夠構建提供高度交互性用戶體驗的分析應用。 商業智慧和儀錶板報告軟體允許您只需單擊幾下即可訪問和組合數據。 您可以連接到結構化和非結構化資料來源,以最少的文本和編碼從多個源聯接表,以及創建互動式 Web 儀表板和報表。 在本文中,您將學習如何將 Azure 資料資源管理器設置為 Sisense 的數據源,以及可視化範例群集中的數據。

## <a name="prerequisites"></a>Prerequisites

完成本文需要以下內容:

* [下載並安裝 Sisense 應用程式](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* 創建包含 StormEvents 範例資料的群集和資料庫。 有關詳細資訊,請參閱[快速入門:建立 Azure 資料資源管理器叢集和資料庫](create-cluster-database-portal.md),並將[範例資料引入 Azure 資料資源管理員](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>使用 Azure 資料資源管理器 JDBC 連接器連接到 Sisense 儀表板

1. 下載以下 jar 檔的最新版本並將其複製到 *.。[Sisense]資料連接器\jdbcdrives\adx* 

    * 啟動-1.1.jar
    * adal4j-1.6.0.jar
    * 公域編解碼器-1.10.jar
    * 公地集合4-4.1.jar
    * 公地朗3-3.5.jar
    * 格森-2.8.0.jar
    * jcip-註釋-1.0-1.jar
    * json-smart-1.3.1.jar
    * 朗標籤-1.4.4.jar
    * 郵件-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * 尼姆布斯-喬瑟-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. 打開**Sisense**應用程式。
1. 選擇 **「資料**」選項卡並選擇 **「ElastiCube」** 以建立新的 ElastiCube 模型。
    
    ![選擇 ElastiCube](media/sisense/data-select-elasticube.png)

1. 新增**新的 ElastiCube 模型**中,命名 ElastiCube 模型並**儲存**。
   
    ![新增 ElastiCube 模型](media/sisense/add-new-elasticube-model.png)

1. 選擇 **= 資料**。

    ![選擇資料按鈕](media/sisense/select-data.png)

1. 在 **「選擇連接器」** 選項卡中,選擇**通用 JDBC**連接器。

    ![選擇 JDBC 連接器](media/sisense/select-connector.png)

1. 在 **「連接」** 選項卡中,完成**通用 JDBC**連接器的以下欄位,然後選擇 **「下一步**」 。。

    ![JDBC 連接器設定](media/sisense/jdbc-connector.png)

    |欄位 |描述 |
    |---------|---------|
    |連接字串     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC JARs 資料夾  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |驅動程式的類別名稱    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |使用者名稱   |    AAD 使用者名稱     |
    |密碼     |   AAD 使用者密碼      |

1. 在**選擇資料**選項卡中,搜尋**選擇資料庫**,以選擇要具有權限的相關資料庫。 這個選項, 選擇*test1*。

    ![選取資料庫](media/sisense/select-database.png)

1. 測試*test*(資料庫名稱)窗格中:
    1. 選擇表名稱以預覽表並查看表列名稱。 您可以刪除不必要的欄。
    1. 選擇相關表的複選框以選擇該表。 
    1. 選取 [完成]  。

    ![選取資料表](media/sisense/select-table-see-columns.png)    

1. 選擇 **「產生」** 以產生資料集。 

    * 在 **「生成」** 視窗中,選擇 **「生成**」 。

      ![產生視窗](media/sisense/build-window.png)

    * 等待生成過程完成,然後選擇 **「生成成功**」。

      ![組建成功](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>建立 Sisense 儀表板

1. 在 **「分析」****+** > 選項卡 中,選擇 **「新建儀錶板**」以在此資料集上創建儀表板。

    ![新增儀表板](media/sisense/new-dashboard.png)

1. 選擇儀錶板並選擇 **「創建**」。 

    ![建立儀表板](media/sisense/create-dashboard.png)

1. 在 **「新小部件**」下,選擇 **+選擇數據**以創建新小部件。 

    ![將欄位新增到風暴事件儀表板](media/sisense/storm-dashboard-add-field.png)  

1. 選擇 **= 新增更多資料**以向圖形添加其他欄。 

    ![新增資料](media/sisense/add-more-data.png)

1. 選擇 **+ 小部件**以建立另一個小部件。 拖放小部件以重新排列儀錶板。

    ![Storm 儀表板](media/sisense/final-dashboard.png)

現在,您可以使用可視化分析流覽數據,構建其他儀表板,並將數據轉換為可操作的見解,從而對您的業務產生影響。

## <a name="next-steps"></a>後續步驟

* [為 Azure 資料資源管理員編寫查詢](write-queries.md)

