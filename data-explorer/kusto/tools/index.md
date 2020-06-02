---
title: Azure 資料總管工具 - Azure 資料總管 | Microsoft Docs
description: 本文說明 Azure 資料總管中的 Azure 資料總管工具。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: b6bc95158c1dd161a17572342c6a99bdf9d37235
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257904"
---
# <a name="azure-data-explorer-tools"></a>Azure 資料總管工具

## <a name="ad-hoc-query-tools"></a>特定查詢工具

* Kusto.Explorer
   * [Kusto.Explorer 安裝和使用者介面](./kusto-explorer.md) - 用於查詢和控制 Kusto 的主要桌面工具
   * [使用 Kusto.Explorer](./kusto-explorer-using.md)
   * [針對 Kusto.Explorer 進行疑難排解](kusto-explorer-troubleshooting.md)
* [Web UI](../../web-query-data.md) - 用於查詢 Kusto 的 Web UI

## <a name="visualizations-dashboards-and-reporting-tools"></a>視覺效果、儀表板和報告工具


* [Azure Notebooks](../../azure-notebooks.md) - 使用 Azure Notebooks 來分析 Azure 資料總管中的資料。
* Excel
    * [Excel 空白查詢](../../excel-blank-query.md) - 將 Kusto 查詢新增為 Excel 資料來源
    * [Excel 連接器](../../excel-connector.md) -適用於 Azure 資料總管的 Excel 連接器 

* PowerBI

   * [PowerBI 最佳做法](../../power-bi-best-practices.md)
   * [PowerBI 連接器](../../power-bi-connector.md)
   * [Power BI 已匯入查詢](../../power-bi-imported-query.md) 
   * [PowerBI SQL 查詢](../../power-bi-sql-query.md)

* [Grafana](../../grafana.md)
* [K2Bridge 開放原始碼連接器](../../k2bridge.md) - 在 Kibana 中從 Azure 資料總管將資料視覺化

## <a name="orchestration-tools"></a>協調流程工具


* Microsoft Flow
    * [Microsoft Flow 連接器](../../flow.md)
    * [Microsoft Flow 連接器使用範例](../../flow-usage.md)
* [Microsoft 邏輯應用程式](./logicapps.md) - 在 [Microsoft 邏輯應用程式](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)中自動執行 Kusto 查詢



## <a name="data-ingestion-tools"></a>資料擷取工具


* [LightIngest](../../lightingest.md) - 用於將特定資料內嵌至 Azure 資料總管的說明公用程式
* [單鍵擷取](../../ingest-data-one-click.md) - 工具可讓您快速內嵌資料，並自動建議資料表和對應結構
* [Azure Data Factory](azure-data-factory.md)


## <a name="source-control-integration-tools"></a>原始檔控制整合工具

* [Azure Pipelines](../../devops.md) - 在您的管線中叫用控制命令
* [同步 Kusto](./synckusto.md)- 將 Kusto 預存函式同步至/自 Git
