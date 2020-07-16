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
ms.openlocfilehash: 166f109f96695380c979dd4060e324187c5b5efc
ms.sourcegitcommit: 4ae1508bbaa8fe9642dcfc8618d77f009bc8ec9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/15/2020
ms.locfileid: "86405465"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Azure 資料總管工具和整合總覽

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流程。 Azure 資料總管會收集、儲存及分析各種資料，以改善產品、增強客戶體驗、監視裝置，以及提升營運效率。 

Azure 資料總管提供不同的工具和整合來進行資料內嵌、查詢、視覺化、協調流程等。 除了其原生服務之外，Azure 資料總管可讓使用者輕鬆地與各種產品和平臺整合、啟用各種客戶使用案例、藉由簡化工作流程來優化商務程式，以及降低成本。 

本文提供 Azure 資料總管工具、連接器和整合的清單，並提供相關檔的連結以取得其他資訊。

## <a name="ingest-data"></a>擷取資料 

資料內嵌是用來將一或多個來源中的資料記錄載入 Azure 資料總管的程式。 一旦擷取之後，資料就會變成可供查詢。 Azure 資料總管為數據內嵌提供數個工具和連接器。 

### <a name="azure-data-explorer-ingestion-tools"></a>Azure 資料總管的內嵌工具

* [LightIngest](lightingest.md) - 用於將特定資料內嵌至 Azure 資料總管的說明公用程式
* 單鍵內嵌
    * [單鍵擷取概觀](ingest-data-one-click.md) 
    * [將資料從容器內嵌至新的資料表](one-click-ingestion-new-table.md)
    * [將資料從本機檔案內嵌至現有的資料表](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>內嵌整合

* 事件中樞
    * [從事件中樞內嵌](kusto/management/data-ingestion/eventhub.md)
    * 使用[Azure 入口網站](ingest-data-event-hub.md)、 [c #](data-connection-event-hub-csharp.md)、 [Python](data-connection-event-hub-python.md)或[Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)從事件中樞內嵌
* 事件方格
    * [從事件方格內嵌](kusto/management/data-ingestion/eventgrid.md)
    * 使用[Azure 入口網站](ingest-data-event-grid.md)、 [c #](data-connection-event-grid-csharp.md)、 [Python](data-connection-event-grid-python.md)或[Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)從事件方格內嵌
* IoT 中樞
    * [從 IoT 中樞內嵌](kusto/management/data-ingestion/iothub.md)
    * 使用[Azure 入口網站](ingest-data-iot-hub.md)、 [c #](data-connection-iot-hub-csharp.md)、 [Python](data-connection-iot-hub-python.md)或[Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)從 IoT 中樞內嵌
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory
    * [與 Azure Data Factory 整合](data-factory-integration.md)
    * [複製資料](data-factory-load-data.md)
    * [使用 Azure Data Factory 範本從資料庫大量複製](data-factory-template.md)
    * [使用 Azure Data Factory 命令活動來執行 Azure 資料總管 control 命令](data-factory-command-activity.md)
* Apache 
    * [Spark](spark-connector.md)
    * [Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>查詢資料

### <a name="azure-data-explorer-query-tools"></a>Azure 資料總管查詢工具

有數個工具可用來在 Azure 資料總管中執行查詢。

* Kusto.Explorer
    * [安裝和使用者介面](kusto/tools/kusto-explorer.md)
    * [使用 Kusto.Explorer](kusto/tools/kusto-explorer-using.md)
    * [options](kusto/tools/kusto-explorer-options.md)
    * 其他主題包括[疑難排解](kusto/tools/kusto-explorer-troubleshooting.md)、[鍵盤快速鍵](kusto/tools/kusto-explorer-shortcuts.md)、程式[代碼重構](kusto/tools/kusto-explorer-refactor.md)、程式[代碼導覽](kusto/tools/kusto-explorer-codenav.md)和程式[代碼分析](kusto/tools/kusto-explorer-code-analyzer.md)
* [Web UI](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>查詢整合

* [Azure 監視器](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Apache Spark](spark-connector.md)
* Microsoft Power Apps
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="visualizations-dashboards-and-reporting"></a>視覺效果、儀表板和報告

[視覺效果總覽](viz-overview.md)會詳細說明資料視覺化、儀表板和報表選項。 

## <a name="notebook-connectivity"></a>筆記本連線能力

* [Azure Notebooks](azure-notebooks.md)
* [Jupyter Notebook](kqlmagic.md)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="orchestration"></a>協調流程

* Power Automate
    * [電源自動化流程連接器](flow.md)
    * [Power Automate 連接器使用範例](flow-usage.md)
* [Microsoft 邏輯應用程式](kusto/tools/logicapps.md) 
* [Azure Data Factory](data-factory-integration.md)

## <a name="share-data"></a>共用資料

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>原始檔控制整合

* [Azure Pipelines](devops.md) 
* [同步 Kusto](kusto/tools/synckusto.md) 

<!--Open Source Tools-->
