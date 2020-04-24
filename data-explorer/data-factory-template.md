---
title: 使用 Azure 資料工廠樣本批次從資料庫複製到 Azure 資料資源管理員
description: 在本文中,您將學習使用 Azure 資料工廠樣本從資料庫批次複製到 Azure 資料資源管理員
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498236"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>使用 Azure 資料工廠樣本批次從資料庫複製到 Azure 資料資源管理員 

Azure 資料資源管理器是一種快速、完全託管的數據分析服務。 它提供來自許多來源(如應用程式、網站和IoT設備)流的大量數據的即時分析。 

要將資料從 Oracle 伺服器、Netezza、Teradata 或 SQL Server 中的資料庫複製到 Azure 資料資源管理器,必須從多個表載入大量資料。 通常,數據必須在每個表中進行分區,以便可以從單個表並行載入具有多個線程的行。 本文介紹要在這些情況下使用的範本。

[Azure 數據工廠範本](/azure/data-factory/solution-templates-introduction)是預定義的數據工廠管道。 這些範本可以説明您快速使用資料工廠,並縮短資料整合專案的開發時間。 

通過使用 *「尋找*」和 *「ForEach」* 活動創建*從資料庫到 Azure 資料資源管理員的批量複本*樣本。 為了加快資料複製速度,可以使用範本為每個資料庫或每個錶創建多個管道。 

> [!IMPORTANT]
> 請務必使用適合要複製的數據量的工具。
> * 使用*從資料庫到 Azure 資料資源管理員的批次複製*樣本將大量資料從資料庫(如 SQL 伺服器和 Google BigQuery)複製到 Azure 資料資源管理器。 
> * 使用[*「資料工廠複製資料」工具*](data-factory-load-data.md)將少數數據量較小或適量的表複製到 Azure 資料資源管理器中。 

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)。
* [建立資料工廠](data-factory-load-data.md#create-a-data-factory)。
* 資料庫中的數據源。

## <a name="create-controltabledataset"></a>建立控制表資料集

*ControlTableDataset*指示將從源複製到管道中的目標的數據。 行數指示複製數據所需的管道總數。 應將 ControlTableDataset 定義為源資料庫的一部分。

SQL Server 源表格式的範例顯示在以下代碼中:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

代碼元素在下表中描述:

|屬性  |描述  | 範例
|---------|---------| ---------|
|PartitionId   |  複製順序 | 1  |  
|來源查詢   |  指示在導管執行時將複製哪些資料的查詢 | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTable 名稱  |  目標表名稱 | MyAdxTable       |  

如果您的 ControlTable 資料集採用不同的格式,請為您的格式建立類似的 ControlTable 資料集。

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>使用從資料庫到 Azure 資料資源管理員的批次複製樣本

1. 在"**讓我們開始「** 窗格中,選擇**從範本建立管道**」 以打開**樣本庫**窗格。

    ![Azure 資料工廠「讓我們開始」窗格](media/data-factory-template/adf-get-started.png)

1. 選擇**從資料庫到 Azure 資料資源管理員樣本的批次 。**
 
    !["從資料庫到 Azure 資料資源管理員的批次複製"樣本](media/data-factory-template/pipeline-from-template.png)

1.  在 **「從資料庫複製到 Azure 資料資源管理員」** 窗格中,在 **「使用者輸入」** 下,透過執行以下操作來指定資料集: 

    a. 在**ControlTableDataset**下拉清單中,選擇連結到控制表的服務,指示從源複製到目標的數據以及數據將放置在目標的位置。 

    b. 在 **「源資料集**」下拉清單中,選擇連結到源資料庫的服務。 

    c. 在**AzureDataExplorerTable**下拉清單中,選擇 Azure 數據資源管理器表。 如果資料集不存在,[請建立 Azure 資料資源管理器連結服務](data-factory-load-data.md#create-the-azure-data-explorer-linked-service)以新增資料集。

    d. 選擇**此樣本**。

    !["從資料庫到 Azure 資料資源管理員的批次複製"窗格](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 選擇畫布中的活動外部區域以訪問範本管道。 選擇 **「參數」** 選項卡以輸入表的參數,包括**名稱**(控制表名稱)和**預設值**(列名稱)。

    ![管線參數](media/data-factory-template/pipeline-parameters.png)

1.  在 **「尋找」** 下,選擇 **「獲取分區清單」** 以查看預設設定。 將自動創建查詢。
1.  選擇「命令活動 **」「每個分割區**」,選擇 **「設定」** 選項卡,然後執行以下操作:

    a. 在 **"批次計數"** 框中,輸入從 1 到 50 的數位。 此選項確定並行運行的管道數,直到達到*ControlTableDataset*行數。 

    b. 為確保管道批次並行運行,*請不要*選擇 **「順序**」 「複選框。

    ![對每個分割區設定](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 最佳做法是並行運行許多管道,以便更快地複製數據。 要提高效率,可以根據日期和表對源表中的數據進行分區,並針對每個管道分配一個分區。

1. 選擇 **「全部驗證**」以驗證 Azure 資料工廠管道,然後在**管道驗證輸出**窗格中查看結果。

    ![驗證樣本導管](media/data-factory-template/validate-template-pipelines.png)

1. 如有必要,選擇 **「調試**」,然後選擇 **「添加觸發器**」以運行管道。

    ![「除錯」與「執行管道」按鈕](media/data-factory-template/trigger-run-of-pipeline.png)    

現在,您可以使用該範本有效地從資料庫和表中複製大量數據。

## <a name="next-steps"></a>後續步驟

* 讓您如何使用[Azure 資料工廠將資料複製到 Azure 資料資源管理員](data-factory-load-data.md)。
* 瞭解 Azure 資料工廠中的[Azure 資料資源管理員連接器](/azure/data-factory/connector-azure-data-explorer)。
* 瞭解[資料查詢的 Azure 資料資源管理員查詢](/azure/data-explorer/web-query-data)。






