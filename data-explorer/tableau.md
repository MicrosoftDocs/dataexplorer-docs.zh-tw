---
title: 使用 Azure 資料總管 ODBC 連接器將 Tableau 資料視覺化
description: 在本文中，您將瞭解如何使用開放式資料庫連接 (ODBC) 與 Azure 資料總管連線的連線，以 Tableau 將資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: f20d580cab401df6f4c39b093d79d7866c22989c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875322"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>在 Tableau 中將 Azure 資料總管中的資料視覺化

 [Tableau](https://www.tableau.com/) 是適用于商業智慧的視覺分析平臺。 若要從 Tableau 連線到 Azure 資料總管，並從範例叢集帶入資料，請使用 (ODBC) 驅動程式 SQL Server 開放式資料庫連接。 

## <a name="prerequisites"></a>先決條件

您需要下列專案才能完成這篇文章：

* 使用 SQL Server ODBC 驅動程式透過[Odbc 連接到 azure 資料總管](connect-odbc.md)，以從 Tableau 連線到 azure 資料總管。 

* Tableau Desktop、full 或 [試用](https://www.tableau.com/products/desktop/download) 版。

* 包含 StormEvents 範例資料的叢集。 如需詳細資訊，請參閱 [建立 azure 資料總管叢集和資料庫](create-cluster-database-portal.md) ，並將 [範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>在 Tableau 中將資料視覺化 

完成 ODBC 設定之後，您就可以將範例資料帶入 Tableau。

1. 在 Tableau Desktop 的左側功能表中，選取 ** (ODBC) 的其他資料庫 **。

    ![與 ODBC 連線](media/tableau/connect-odbc.png)

1. 針對 **DSN**，選取您為 ODBC 建立的資料來源，然後選取 [登 **入**]。

    ![ODBC 登入](media/tableau/odbc-sign-in.png)

1. 針對 [ **資料庫**]，選取您範例叢集上的資料庫，例如 [ *>testdatabase*]。 針對 [ **架構**] 選取 [ *dbo*]，然後選取 [ **資料表**] 的 [ *StormEvents* ] 範例資料表。

    ![選取資料庫和資料表](media/tableau/select-database-table.png)

1. Tableau 現在會顯示範例資料的架構。 選取 [ **立即更新** ] 以將資料帶入 Tableau。

    ![更新資料](media/tableau/update-data.png)

    匯入資料時，Tableau 會顯示類似下圖的資料列。

    ![結果集](media/tableau/result-set.png)

1. 現在您可以根據您從 Azure 資料總管中導入的資料，在 Tableau 中建立視覺效果。 如需詳細資訊，請參閱 [Tableau 學習](https://www.tableau.com/learn)。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)