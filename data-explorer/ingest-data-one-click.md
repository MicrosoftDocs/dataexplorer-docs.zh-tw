---
title: 使用單鍵擷取將資料內嵌到 Azure 資料總管資料
description: 使用單鍵擷取輕鬆將資料內嵌 (載入) 到 Azure 資料總管的概觀。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: overview
ms.date: 03/29/2020
ms.openlocfilehash: 16db3eb2d925d12ba8eefa605b27566e9b4c1b04
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373809"
---
# <a name="what-is-one-click-ingestion"></a>什麼是單鍵擷取？ 

單鍵擷取可讓您快速內嵌資料，並根據 Azure 資料總管中的資料來源，自動建議資料表和對應結構。 

使用 Azure 資料總管 Web UI，即可從儲存體 (Blob 檔案)、本機檔案或容器 (最高可達 10,000 Blob) 內嵌資料。 您也可以在容器上定義事件方格以進行連續擷取。 可以使用 JSON、CSV 和[其他格式](#file-formats)將資料內嵌至現有或新的資料表。 單鍵擷取可以根據資料來源建議新資料表和資料表對應的結構，並提供直覺化的平台來調整資料表對應和現有或新資料表的資料表結構。 只要按一下，就能在短短幾分鐘內將資料內嵌到資料表中。

第一次內嵌資料時，或不熟悉資料的結構描述時，單鍵擷取特別有用。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)。
* 登入 [Azure 資料總管 Web UI](https://dataexplorer.azure.com/) 並[將連線新增至您的叢集](web-query-data.md#add-clusters)。

## <a name="file-formats"></a>檔案格式

單鍵擷取支援以下列任何格式從來源資料內嵌新的資料表：
* JSON
* CSV
* TSV
* SCSV
* SOHSV
* TSVE
* PSV

## <a name="one-click-ingestion-wizard"></a>單鍵擷取精靈

單鍵擷取精靈會引導您完成單鍵擷取流程。 

> [!Note]
> 本節約略說明精靈內容。 請依要內嵌至新的或現有的資料表來選取選項。 如需詳細資訊，請參閱
    > * 內嵌到[新的資料表](one-click-ingestion-new-table.md)
    > * 內嵌到[現有的資料表](one-click-ingestion-existing-table.md) 
    
1. 若要存取精靈，請以滑鼠右鍵按一下 Azure 資料總管 web UI 左側功能表中的*資料庫*或*資料表*列，然後選取 [內嵌新資料 (預覽)]  。

    ![在 Web UI 中選取單鍵擷取](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   

1. 此精靈會引導您從下列選項中選取：
    * 內嵌到[現有的資料表](one-click-ingestion-existing-table.md)
    * 內嵌到[新的資料表](one-click-ingestion-new-table.md)
    * 內嵌資料來源：
      * Blob 儲存體
      * 本機檔案
      * 容器
    * 輸入從 1 到 10,000 個列的樣本大小 (僅限來自容器)
       
1. 成功選取資料來源後，就會顯示資料的預覽。 
    如果要從容器中內嵌資料，您可以篩選資料，以便只內嵌具有特定前置詞或副檔名的檔案。 例如，您可能只想要內嵌開頭為單字*歐洲*的檔案名，或僅內嵌副檔名為 .json  的檔案。 

1. 按一下 [編輯結構描述]  。 檢閱並調整自動產生的結構描述和內嵌屬性 (資料格式、對應名稱等)

1. 啟動資料擷取流程。

> [!Note]
> 如果您的資料來源是容器，請注意 Azure 資料總管具有資料擷取的彙總 (批次處理) 原則，可將擷取程序最佳化。 此原則已預設為 5 分鐘或 500 MB 的資料，因此您可能會遇到延遲情況。 如需了解匯總選項，請參閱[批次原則](kusto/management/batchingpolicy.md)。 從其他來源內嵌資料時，擷取會立即生效。

## <a name="next-steps"></a>後續步驟

* 決定您是否要使用單鍵擷取，將資料內嵌至[現有資料表](one-click-ingestion-existing-table.md)或[新的資料表](one-click-ingestion-new-table.md)
* [在 Azure 資料總管 Web UI 中查詢資料](web-query-data.md)
* [使用 Kusto 查詢語言撰寫 Azure 資料總管的查詢](write-queries.md)
