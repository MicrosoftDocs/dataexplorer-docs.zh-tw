---
title: Azure 資料資源管理員資料視覺化
description: 讓視覺化 Azure 資料資源管理員資料的不同方式
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 6d3b692d72b673b55e4bdc0f737b74b9c4a669d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500784"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 資料資源管理員進行資料視覺化 

Azure 資料資源管理器是一種用於日誌和遙測數據的快速且高度可擴展的數據探索服務,用於為大量資料構建複雜的分析解決方案。 Azure 資料資源管理器與各種視覺化工具整合,因此您可以可視化資料並在整個組織中共享結果。 這些數據可以轉換為可操作的見解,對您的業務產生影響。

數據可視化和報告是數據分析過程中的關鍵步驟。 Azure 資料資源管理器支援許多 BI 服務,因此可以使用最適合方案和預算的服務。

## <a name="kusto-query-language-visualizations"></a>函式庫斯托查詢語言視覺化

Kusto 查詢[`render operator`](kusto/query/renderoperator.md)語言 提供各種可視化效果,如表格、餅圖和條形圖,用於描述查詢結果。 查詢可視化有助於異常檢測和預測、機器學習等。

## <a name="power-bi"></a>Power BI

Azure 資料資源管理員提供使用各種方法連線到[Power BI](https://powerbi.microsoft.com)的功能: 

  * [內建本機電源 BI 連接器](/azure/data-explorer/power-bi-connector)

  * [從 Azure 資料資源管理員匯入電源 BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL 查詢 (SQL query)](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 資料資源管理員提供使用[內建本機 Excel 連接器](excel-connector.md)連接到[Microsoft Excel](https://products.office.com/excel)的功能,或將查詢從 Azure 資料資源管理器[導入](excel-blank-query.md)到 Excel 中。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com)提供了 Azure 資料資源管理器外掛程式,使您能夠可視化來自 Azure 資料資源管理員的數據。 [將 Azure 資料資源管理員設定為 Grafana 的資料來源,然後可視化資料](/azure/data-explorer/grafana)。 

## <a name="kibana"></a>Kibana

Azure 資料資源管理員提供使用開源連接器 K2Bridge 連接到[Kibana(發現頁面)](https://www.elastic.co/guide/en/kibana/6.8/discover.html)的功能。 [將 Azure 資料資源管理員設定為 Kibana 的資料來源,然後可視化資料](/azure/data-explorer/k2bridge)。

## <a name="odbc-connector"></a>ODBC 連接器

Azure 資料資源管理員提供[開放資料庫連接 (ODBC) 連接器](connect-odbc.md),因此任何支援 ODBC 的應用程式都可以連接到 Azure 資料資源管理器。

## <a name="tableau"></a>Tableau

Azure 資料資源管理員提供使用[ODBC 連接器](/azure/data-explorer/connect-odbc)連線到[Tableau](https://www.tableau.com)的功能,然後在[Tableau 中可視化資料](tableau.md)。

## <a name="qlik"></a>Qlik

Azure 資料資源管理員提供了使用[ODBC 連接器](/azure/data-explorer/connect-odbc)連接到[Qlik](https://www.qlik.com)的功能,然後創建 Qlik Sense 儀表板並可視化數據。 使用以下視頻,可以學習使用 Qlik 視覺化 Azure 資料資源管理器資料。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 資料資源管理員提供了使用 JDBC 連接器連接到[Sisense](https://www.sisense.com)的功能。 [將 Azure 資料資源管理員設定為 Sisense 的資料來源,然後可視化資料](/azure/data-explorer/sisense)。

## <a name="redash"></a>Redash

您可以使用[Redash](https://redash.io/)生成儀表板和可視化數據。 [將 Azure 資料資源管理員設定為 Redash 的資料來源,然後可視化資料](/azure/data-explorer/redash)。