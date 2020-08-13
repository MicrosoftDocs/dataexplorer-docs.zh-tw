---
title: 將資料從 IoT 中樞內嵌到 Azure 資料總管
description: 在本文中，您將瞭解如何將) 資料的 (載入至 Azure 資料總管從 IoT 中樞。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 36c724a001bb4438757316a456fbf85b55691c09
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201594"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>將資料從 IoT 中樞內嵌到 Azure 資料總管 

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

本文說明如何將資料從 IoT 中樞（big data 串流平臺和 IoT 內嵌服務）內嵌到 Azure 資料總管。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立具有資料庫名稱*testdb*[的測試叢集和資料庫](create-cluster-database-portal.md)。
* 用於模擬裝置的[範例應用程式](https://github.com/Azure-Samples/azure-iot-samples-csharp)和檔。
* 用於執行範例應用程式的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

## <a name="create-an-iot-hub"></a>建立 Iot 中樞

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>向 IoT 中樞註冊裝置

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>在 Azure 資料總管中建立目標資料表

現在，您會在 Azure 資料總管中建立一個 IoT 中樞將用來傳送資料的資料表。 您會在叢集內建立資料表，並在 [**必要條件**](#prerequisites)中布建資料庫。

1. 在 Azure 入口網站中瀏覽至您的叢集，然後選取 [查詢]****。

    ![入口網站中的 ADX 查詢](media/ingest-data-iot-hub/adx-initiate-query.png)

1. 將下列命令複製到視窗，然後選取 [執行]**** 以建立資料表 (TestTable)，該資料表會接收內嵌的資料。

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![執行建立查詢](media/ingest-data-iot-hub/run-create-query.png)

1. 將下列命令複製到視窗中，然後選取 [執行]**** 以將傳入的 JSON 資料對應至資料表 (TestTable) 的資料行名稱與資料類型。

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>將 Azure 資料總管資料表連線到 IoT 中樞

現在，您會從 Azure 資料總管連線到 IoT 中樞。 當此連線完成時，流入 iot 中樞的資料會串流至 [您所建立的目標資料表](#create-a-target-table-in-azure-data-explorer)。

1. 選取工具列上的 [ **通知** ]，確認 IoT 中樞部署成功。

1. 在您建立的叢集下方，選取 [ **資料庫** ]，然後選取您所建立的 **testdb**資料庫。
    
    ![選取測試資料庫](media/ingest-data-iot-hub/select-database.png)

1. 選取 [資料擷取]****，然後選取 [新增資料連線]****。 然後，在表單中填寫以下資訊。 當您完成時，請選取 [ **建立** ]。

    ![IoT 中樞連接](media/ingest-data-iot-hub/iot-hub-connection.png)

    **資料來源**：

    **設定** | **欄位描述**
    |---|---|
    | 資料連線名稱 | 您想要在 Azure 中建立的連線名稱資料總管
    | IoT 中樞 | IoT 中樞名稱 |
    | 共用存取原則 | 共用存取原則的名稱。 必須擁有讀取權限 |
    | 取用者群組 |  在 IoT 中樞內建端點中定義的取用者群組 |
    | 事件系統屬性 | [IoT 中樞事件系統屬性](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages)。 加入系統屬性時， [建立](kusto/management/create-table-command.md) 或 [更新](kusto/management/alter-table-command.md) 資料表架構和 [對應](kusto/management/mappings.md) 以包含選取的屬性。 | | | 

    > [!NOTE]
    > 在 [手動容錯移轉](/azure/iot-hub/iot-hub-ha-dr#manual-failover)的情況下，您必須重新建立資料連線。

    **目標資料表**：

    路由內嵌資料有兩個選項：靜態** 和動態**。 
    在本文中，您將使用靜態路由，您會指定資料表名稱、資料格式和對應。 因此，將 [我的資料包含路由資訊]**** 保留為未選取。

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | Table | *TestTable* | 您在 **testdb**中建立的資料表。 |
    | 資料格式 | *JSON* | 支援的格式為 Avro、CSV、JSON、多行 JSON、ORC、PARQUET、PSV、SCSV、SOHSV、TSV、TXT、TSVE、APACHEAVRO 和的 W3CLOG。|
    | 資料行對應 | *TestMapping* | 您在**testdb**中建立的[對應](kusto/management/mappings.md)，其會將傳入的 JSON 資料對應至**testdb**的資料行名稱和資料類型。 對 JSON、多行 JSON 和 AVRO 而言是必要的，而且對於其他格式則為選擇性。|
    | | |

    > [!NOTE]
    > * 選取 [我的資料包含路由資訊]**** 來使用動態路由，其中您的資料會包含必要的路由資訊，如[範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)註解中所示。 如果靜態和動態屬性都已設定，則動態屬性會覆寫靜態屬性。 
    > * 只有在建立資料連線之後排入佇列的事件才會內嵌。

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>產生測試用的範例資料

模擬裝置應用程式會連線到 IoT 中樞上的裝置特定端點，並且傳送模擬的溫度和溼度遙測。

1. 從 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip 下載範例 C# 專案並將 ZIP 封存檔解壓縮。

1. 在本機終端機視窗中，瀏覽至範例 C# 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.cs** 檔案。

    將變數的值取代為 [ `s_connectionString` [向 IoT 中樞註冊裝置](#register-a-device-to-the-iot-hub)] 中的裝置連接字串。 然後將變更儲存到 **SimulatedDevice.cs** 檔案。

1. 在本機終端機視窗中，執行下列命令以安裝模擬裝置應用程式所需的套件：

    ```cmd/sh
    dotnet restore
    ```

1. 在本機終端機視窗中，執行下列命令以建置並執行模擬裝置應用程式：

    ```cmd/sh
    dotnet run
    ```

    下列螢幕擷取畫面顯示模擬裝置應用程式將遙測傳送到 IoT 中樞時的輸出：

    ![執行模擬的裝置](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>檢閱資料流程

當應用程式產生資料時，您現在可以看到來自 IoT 中樞的資料流程到叢集中的資料表。

1. 在 [Azure 入口網站] 的 IoT 中樞底下，您會在應用程式執行時看到活動尖峰。

    ![IoT 中樞計量](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. 若要檢查目前為止已有多少則訊息成功進入資料庫，請在測試資料庫中執行下列查詢。

    ```Kusto
    TestTable
    | count
    ```

1. 若要查看訊息的內容，請執行下列查詢：

    ```Kusto
    TestTable
    ```

    結果集：
    
    ![顯示內嵌資料結果](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure 資料總管具有資料擷取的彙總 (批次處理) 原則，可將擷取程序最佳化。 根據預設，原則設定為5分鐘或 500 MB 的資料，因此您可能會遇到延遲。 如需了解匯總選項，請參閱[批次原則](kusto/management/batchingpolicy.md)。 
    > * 設定資料表以支援串流處理，並移除回應時間的延遲。 請參閱 [串流原則](kusto/management/streamingingestionpolicy.md)。 

## <a name="clean-up-resources"></a>清除資源

如果您不打算再次使用您的 IoT 中樞，請清除您的資源群組，以避免產生成本。

1. 在 Azure 入口網站中選取靠左側的 [資源群組]****，然後選取您所建立的群組。  

    如果左側功能表已摺疊，請選取 ![[展開] 按鈕](media/ingest-data-event-hub/expand.png) 加以展開。

   ![選取要刪除的資源群組](media/ingest-data-iot-hub/delete-resources-select.png)

1. 在 [test-resource-group]**** 下方，選取 [刪除資源群組]****。

2. 在新視窗中，輸入要刪除之資源群組的名稱，然後選取 [ **刪除**]。

## <a name="next-steps"></a>後續步驟

* [查詢 Azure 中的資料資料總管](web-query-data.md)
