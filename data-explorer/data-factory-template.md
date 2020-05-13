---
title: 使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管
description: 在本文中，您將瞭解如何使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: e1c51132ddb36a206a97236b6ceb4553a62e6d2c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373922"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管 

Azure 資料總管是一種快速、完全受控的資料分析服務。 它會針對從許多來源（例如應用程式、網站和 IoT 裝置）串流的大量資料提供即時分析。 

若要將資料從 Oracle 伺服器中的資料庫、Netezza、Teradata 或 SQL Server 複製到 Azure 資料總管，您必須從多個資料表載入大量資料。 通常，資料必須在每個資料表中進行分割，如此一來，您就可以從單一資料表以平行方式載入具有多個執行緒的資料列。 本文說明在這些案例中使用的範本。

[Azure Data Factory 範本](/azure/data-factory/solution-templates-introduction)是預先定義的 Data Factory 管線。 這些範本可協助您快速開始使用 Data Factory，並縮短資料整合專案的開發時間。 

您可以使用*查閱*和*ForEach*活動，*從資料庫建立大量複製到 Azure 資料總管*範本。 若要更快速地複製資料，您可以使用範本，針對每個資料庫或每個資料表建立許多管線。 

> [!IMPORTANT]
> 請務必使用適用于您想要複製之資料數量的工具。
> * 使用*從資料庫大量複製到 azure 資料總管*範本，將大量資料從 SQL Server 和 Google BigQuery 之類的資料庫複製到 azure 資料總管。 
> * 使用[*Data Factory 資料複製工具*](data-factory-load-data.md)，將幾個具有小型或適中資料量的資料表複製到 Azure 資料總管。 

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
* [建立資料](data-factory-load-data.md#create-a-data-factory)處理站。
* 資料庫中的資料來源。

## <a name="create-controltabledataset"></a>建立 ControlTableDataset

*ControlTableDataset*指出將從來源複製到管線中目的地的資料。 資料列數目表示複製資料所需的管線總數。 您應該將 ControlTableDataset 定義為源資料庫的一部分。

下列程式碼顯示 SQL Server 來源資料表格式的範例：
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

下表描述程式碼元素：

|屬性  |描述  | 範例
|---------|---------| ---------|
|PartitionId   |  複製順序 | 1  |  
|SourceQuery   |  指出管線執行時間期間將複製哪些資料的查詢 | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  目的地資料表名稱 | MyAdxTable       |  

如果您的 ControlTableDataset 使用不同的格式，請為您的格式建立可比較的 ControlTableDataset。

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>使用從資料庫到 Azure 資料總管範本的大量複製

1. 在 [**現在就開始**吧] 窗格中，選取 [**從範本建立管線**] 以開啟 [**範本資源庫**] 窗格。

    ![Azure Data Factory 「讓我們開始使用」窗格](media/data-factory-template/adf-get-started.png)

1. 選取 [**從資料庫大量複製到 Azure 資料總管**] 範本。
 
    ![「從資料庫大量複製到 Azure 資料總管」範本](media/data-factory-template/pipeline-from-template.png)

1.  在 [**從資料庫大量複製到 Azure 資料總管**] 窗格的 [**使用者輸入**] 底下，執行下列動作以指定您的資料集： 

    a. 在 [ **ControlTableDataset** ] 下拉式清單中，選取 [控制資料表] 的連結服務，以指出從來源複製到目的地的資料，以及放置於目的地的位置。 

    b. 在 [ **SourceDataset** ] 下拉式清單中，選取源資料庫的連結服務。 

    c. 在 [ **AzureDataExplorerTable** ] 下拉式清單中，選取 [Azure 資料總管] 資料表。 如果資料集不存在，請[建立 Azure 資料總管連結服務](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)以新增資料集。

    d. 選取 [**使用此範本**]。

    ![[從資料庫大量複製到 Azure 資料總管] 窗格](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 在 [活動] 外的畫布中選取一個區域，以存取範本管線。 選取 [**參數**] 索引標籤，以輸入資料表的參數，包括**名稱**（控制資料表名稱）和**預設值**（資料行名稱）。

    ![管線參數](media/data-factory-template/pipeline-parameters.png)

1.  在 [**查閱**] 底下，選取 [ **GetPartitionList** ] 以查看預設設定。 系統會自動建立查詢。
1.  選取 [命令] 活動， **ForEachPartition**，選取 [**設定**] 索引標籤，然後執行下列動作：

    a. 在 [**批次計數**] 方塊中，輸入介於1到50之間的數位。 此選項會決定以平行方式執行的管線數目，直到達到*ControlTableDataset*的資料列數目為止。 

    b. 若要確保管線批次以平行方式執行，*請勿*選取 [**順序**] 核取方塊。

    ![ForEachPartition 設定](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 最佳做法是平行執行許多管線，讓您的資料可以更快速地複製。 若要提高效率，請在來源資料表中分割資料，並根據日期和資料表配置每個管線的一個分割區。

1. 選取 [**全部驗證**] 以驗證 Azure Data Factory 管線，然後在 [**管線驗證輸出**] 窗格中查看結果。

    ![驗證範本管線](media/data-factory-template/validate-template-pipelines.png)

1. 如有需要，請選取 [ **Debug**]，然後選取 [**新增觸發**程式] 來執行管線。

    ![[Debug] 和 [執行管線] 按鈕](media/data-factory-template/trigger-run-of-pipeline.png)    

您現在可以使用範本，從資料庫和資料表有效率地複製大量資料。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[使用 Azure Data Factory 將資料複製到 Azure 資料總管](data-factory-load-data.md)。
* 瞭解 Azure Data Factory 中的[Azure 資料總管連接器](/azure/data-factory/connector-azure-data-explorer)。
* 瞭解用於資料查詢的[Azure 資料總管查詢](web-query-data.md)。






