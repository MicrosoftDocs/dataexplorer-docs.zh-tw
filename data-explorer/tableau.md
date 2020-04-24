---
title: 使用 Azure 資料資源管理員 ODBC 連接器視覺化 Tableau 資料
description: 在本文中,您將瞭解如何使用開放資料庫連接(ODBC) 連接到 Azure 資料資源管理器連接,以便使用 Tableau 視覺化資料。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5fc98a0bb173bc926dc289b16c1aa8d1a0f5d9de
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501122"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>在 Tableau 中視覺化來自 Azure 資料資源管理員的資料

 [Tableau](https://www.tableau.com/)是一個用於商業智慧的視覺化分析平臺。 要從 Tableau 連接到 Azure 資料資源管理員並從範例群集中引入資料,請使用 SQL Server 開放資料庫連接 (ODBC) 驅動程式。 

## <a name="prerequisites"></a>Prerequisites

完成本文需要以下內容:

* 使用 SQL Server [ODBC 驅動程式使用 ODBC 連接到 Azure 資料資源管理員](connect-odbc.md),以便從 Tableau 連接到 Azure 資料資源管理員。 

* Tableau 桌面、完整版或[試用](https://www.tableau.com/products/desktop/download)版。

* 包含 StormEvents 範例資料的叢集。 有關詳細資訊,請參閱建立[Azure 資料資源管理器叢集和資料庫](create-cluster-database-portal.md),並將[範例資料引入到 Azure 資料資源管理員中](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>在 Tableau 中視覺化資料 

完成部署 ODBC 後,可以將範例資料引入 Tableau。

1. 在 Tableau 桌面中,在左側選單中,選擇**其他資料庫 (ODBC)。**

    ![與 ODBC 連線](media/tableau/connect-odbc.png)

1. 對於**DSN,** 選擇為 ODBC 創建的資料來源,然後選擇 **「登錄**」 。

    ![ODBC 登入](media/tableau/odbc-sign-in.png)

1. 對於**資料庫**,選擇範例群集上的資料庫,如*測試資料庫*。 對於**架構**,選擇*dbo*,對於**表**,請選擇*StormEvents*範例表。

    ![選擇資料庫與表格](media/tableau/select-database-table.png)

1. Tableau 現在顯示範例數據的架構。 選擇 **「立即更新」** 以將資料引入 Tableau。

    ![更新資料](media/tableau/update-data.png)

    匯入資料時,Tableau 將顯示類似於下圖的數據列。

    ![結果集](media/tableau/result-set.png)

1. 現在,您可以根據從 Azure 資料資源管理器引入的數據在 Tableau 中建立可視化效果。 有關詳細資訊,請參閱[Tableau 學習](https://www.tableau.com/learn)。

## <a name="next-steps"></a>後續步驟

* [為 Azure 資料資源管理員編寫查詢](write-queries.md)