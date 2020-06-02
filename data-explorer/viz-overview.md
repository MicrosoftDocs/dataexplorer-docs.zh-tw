---
title: Azure 資料總管資料視覺效果
description: 瞭解您可以將 Azure 資料總管資料視覺化的不同方式
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: b1351ceb9fe4b81a818ca41728a588dddfb4c5a2
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294673"
---
# <a name="data-visualization-with-azure-data-explorer"></a>使用 Azure 資料總管的資料視覺效果 

Azure 資料總管是快速且可高度調整的資料探索服務，適用于記錄和遙測資料，用來建立大量資料的複雜分析解決方案。 Azure 資料總管與各種視覺效果工具整合，因此您可以將資料視覺化，並在整個組織中共用結果。 此資料可以轉換成可操作的深入解析，以對您的企業產生影響。

資料視覺效果和報告是資料分析程式中的重要步驟。 Azure 資料總管支援許多 BI 服務，因此您可以使用最適合您案例和預算的服務。

## <a name="azure-data-explorer-dashboards"></a>Azure Data Explorer 儀表板

Azure 資料總管儀表板是一個 web 應用程式，可讓您在獨立 web 應用程式（ [WEB UI](web-query-data.md)）中執行查詢並建立儀表板。 Azure 資料總管儀表板提供三個主要優點：

* 以原生方式將查詢從 Web UI 匯出至 Azure 資料總管儀表板。 
* 流覽 Web UI 中的資料。
* 優化儀表板轉譯效能。

如需詳細資訊，請參閱[使用 Azure 資料總管儀表板將資料視覺化](azure-data-explorer-dashboards.md)。

## <a name="kusto-query-language-visualizations"></a>Kusto 查詢語言視覺效果

Kusto 查詢語言 [`render operator`](kusto/query/renderoperator.md) 提供各種視覺效果（例如資料表、圓形圖和橫條圖）來描述查詢結果。 查詢視覺效果適用于異常偵測和預測、機器學習等等。

## <a name="power-bi"></a>Power BI

Azure 資料總管提供使用各種方法連接到[Power BI](https://powerbi.microsoft.com)的功能： 

  * [內建的原生 Power BI 連接器](power-bi-connector.md)

  * [從 Azure 資料總管到 Power BI 的查詢匯入](power-bi-imported-query.md)
 
  * [SQL 查詢](power-bi-sql-query.md)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure 資料總管可讓您使用[內建的原生 Excel 連接器](excel-connector.md)連接到[Microsoft Excel](https://products.office.com/excel) ，或將[查詢](excel-blank-query.md)從 Azure 資料總管匯入 Excel 中。

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com)提供 azure 資料總管外掛程式，可讓您將 azure 資料總管的資料視覺化。 您會[將 Azure 資料總管設定為 Grafana 的資料來源，然後將資料視覺化](grafana.md)。 

## <a name="kibana"></a>Kibana

Azure 資料總管提供使用 K2Bridge （開放原始碼連接器）連接到[Kibana](https://www.elastic.co/guide/en/kibana/6.8/discover.html)的功能。 您會[將 Azure 資料總管設定為 Kibana 的資料來源，然後將資料視覺化](k2bridge.md)。

## <a name="odbc-connector"></a>ODBC 連接器

Azure 資料總管提供[開放式資料庫連接（ODBC）連接器](connect-odbc.md)，因此任何支援 ODBC 的應用程式都可以連接到 Azure 資料總管。

## <a name="tableau"></a>Tableau

Azure 資料總管提供使用[ODBC 連接器](connect-odbc.md)連線到[Tableau](https://www.tableau.com)的功能，然後將[Tableau 中的資料視覺化](tableau.md)。

## <a name="qlik"></a>Qlik

Azure 資料總管提供使用[ODBC 連接器](connect-odbc.md)連接到[qlik sense](https://www.qlik.com)的功能，然後建立 qlik sense 感知儀表板並將資料視覺化。 使用下列影片，您可以瞭解如何使用 Qlik sense 將 Azure 資料總管資料視覺化。 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure 資料總管提供使用 JDBC 連接器連接到[Sisense](https://www.sisense.com)的功能。 您會[將 Azure 資料總管設定為 Sisense 的資料來源，然後將資料視覺化](sisense.md)。

## <a name="redash"></a>Redash

您可以使用[Redash](https://redash.io/)來建立儀表板，並將資料視覺化。 [將 Azure 資料總管設定為 Redash 的資料來源，然後將資料視覺化](redash.md)。
