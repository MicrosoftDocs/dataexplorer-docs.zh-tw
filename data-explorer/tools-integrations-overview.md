---
title: Azure 資料總管工具和整合總覽-Azure 資料總管
description: 本文說明 Azure 資料總管中的工具和整合。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 2bce9020161e6b26a0df07b306259589c43b62f3
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644649"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Azure 資料總管工具和整合總覽

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料。 Azure 資料總管會收集、儲存及分析各種不同的資料，以改善產品、增強客戶體驗、監視裝置，以及提升營運效率。 

Azure 資料總管提供不同的工具和整合，以進行資料內嵌、查詢、視覺效果、協調流程等等。 除了其原生服務，Azure 資料總管可讓使用者輕鬆地與各種產品和平臺整合、提供各種客戶使用案例、簡化工作流程，並降低成本。 

本文提供 Azure 資料總管工具、連接器和整合的清單，以及相關檔的連結，以取得其他資訊。

## <a name="ingest-data"></a>擷取資料 

資料內嵌是用來將資料記錄從一或多個來源載入至 Azure 資料總管的程式。 一旦擷取之後，資料就會變成可供查詢。 Azure 資料總管針對資料內嵌提供數個工具和連接器。 

### <a name="azure-data-explorer-ingestion-tools"></a>Azure 資料總管內嵌工具

* [LightIngest](lightingest.md) - 用於將特定資料內嵌至 Azure 資料總管的說明公用程式
* 單鍵內嵌： [總覽](ingest-data-one-click.md) 和將資料 [從容器內嵌至新的資料表](one-click-ingestion-new-table.md) ，或 [從本機檔案內嵌至現有的資料表](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>內嵌整合

* 事件中樞： [從事件中樞內嵌總覽](ingest-data-event-hub-overview.md) 和使用 [Azure 入口網站](ingest-data-event-hub.md)、 [c #](data-connection-event-hub-csharp.md)、 [Python](data-connection-event-hub-python.md) 或 [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)
* 事件方格： [從事件方格中內嵌總覽](ingest-data-event-grid-overview.md) 和使用 [Azure 入口網站](ingest-data-event-grid.md)、 [c #](data-connection-event-grid-csharp.md)、 [Python](data-connection-event-grid-python.md) 或 [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)
* Iot 中樞： [從 iot 中樞內嵌總覽](ingest-data-iot-hub-overview.md) 和使用 [Azure 入口網站](ingest-data-iot-hub.md)、 [c #](data-connection-iot-hub-csharp.md)、 [Python](data-connection-iot-hub-python.md) 或 [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory： [整合總覽](data-factory-integration.md)、 [複製資料](data-factory-load-data.md)、 [使用 Azure Data Factory 範本大量複製](data-factory-template.md)，以及 [使用 Azure Data Factory 命令活動執行 Azure 資料總管控制命令](data-factory-command-activity.md)
* [Azure Synapse Apache Spark](/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Apache Spark](spark-connector.md)
* [Apache Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>查詢資料

### <a name="azure-data-explorer-query-tools"></a>Azure 資料總管查詢工具

有幾個工具可在 Azure 資料總管中執行查詢。

* Kusto.Explorer
    * [使用 Kusto](kusto/tools/kusto-explorer-using.md)的[安裝和使用者介面](kusto/tools/kusto-explorer.md)
    * 其他主題包括 [選項](kusto/tools/kusto-explorer-options.md)、 [疑難排解](kusto/tools/kusto-explorer-troubleshooting.md)、 [鍵盤快速鍵](kusto/tools/kusto-explorer-shortcuts.md)、程式 [代碼重構](kusto/tools/kusto-explorer-refactor.md)、程式碼 [導覽](kusto/tools/kusto-explorer-codenav.md)和程式 [代碼分析](kusto/tools/kusto-explorer-code-analyzer.md)
* [Web UI](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-open-source-repositories"></a>查詢開放原始碼存放庫

下列開放原始碼存放庫包含 Kusto Query Lanuguage (KQL) parser 和 editor 的原始程式碼：

* [Kusto 查詢語言](https://github.com/microsoft/Kusto-Query-Language) 剖析器-Kusto 查詢語言剖析器和語義樹狀結構的 .net core 存放庫。
* [適用于摩納哥編輯器的 Kusto 查詢語言外掛程式](https://github.com/Azure/monaco-kusto) -可內嵌在 web 和 electron 型應用程式中的 Kusto 查詢語言編輯器。 

### <a name="query-integrations"></a>查詢整合

* [Azure 監視器](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Azure Synapse Apache Spark](/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Apache Spark](spark-connector.md)
* [Microsoft Power Apps](power-apps-connector.md)
* Azure Data Studio： [Kusto 延伸模組總覽](/sql/azure-data-studio/extensions/kusto-extension?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)、 [使用 Kusto](/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)，以及 [使用 Kqlmagic](/sql/azure-data-studio/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)

## <a name="visualizations-dashboards-and-reporting"></a>視覺效果、儀表板和報告

[視覺效果總覽](viz-overview.md)會詳細說明資料視覺效果、儀表板和報表選項。 

## <a name="notebook-connectivity"></a>筆記本連線能力

* [Azure Notebooks](/sql/azure-data-studio/notebooks/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext%253fcontext%253d%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Jupyter Notebook](kqlmagic.md)
* Azure Data Studio： [Kusto 延伸模組總覽](/sql/azure-data-studio/extensions/kusto-extension?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)、 [使用 Kusto](/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)，以及 [使用 Kqlmagic](/sql/azure-data-studio/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)

## <a name="orchestration"></a>協調流程

* Power Automate
    * [Power Automate 連接器](flow.md)
    * [Power Automate 連接器使用範例](flow-usage.md)
* [Microsoft 邏輯應用程式](kusto/tools/logicapps.md) 
* [Azure Data Factory](data-factory-integration.md)

## <a name="share-data"></a>共用資料

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>原始檔控制整合

* [Azure Pipelines](devops.md) 
* [同步 Kusto](kusto/tools/synckusto.md) 

<!--Open Source Tools-->
