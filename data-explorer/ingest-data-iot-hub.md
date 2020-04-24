---
title: 將資料從 IoT 中心引入 Azure 資料資源管理員
description: 在本文中,您將瞭解如何從 IoT 中心將資料引入(載入)到 Azure 資料資源管理器中。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1e04b3c2796f8d8814e21763d9a62189b4dc06cb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500381"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>將資料從 IoT 中心引入 Azure 資料資源管理員 

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料資源管理員提供來自 IoT 中心(大數據流平臺和 IoT 引入服務)的引入(數據載入)。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 使用資料庫名稱*testdb*建立[測試叢集與資料庫](create-cluster-database-portal.md)。
* 用於類比設備[的範例應用](https://github.com/Azure-Samples/azure-iot-samples-csharp)和文件。
* 用於執行範例應用程式的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

## <a name="create-an-iot-hub"></a>建立 Iot 中心

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>將裝置註冊到 IoT 中心

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>在 Azure 資料總管中建立目標資料表

現在,您可以在 Azure 資料資源管理器中創建一個表,IoT 中心將向其發送數據。 在群集中創建表,並在[**先決條件**](#prerequisites)中預配資料庫。

1. 在 Azure 入口網站中瀏覽至您的叢集，然後選取 [查詢]****。

    ![門戶中的 ADX 查詢](media/ingest-data-iot-hub/adx-initiate-query.png)

1. 將下列命令複製到視窗，然後選取 [執行]**** 以建立資料表 (TestTable)，該資料表會接收內嵌的資料。

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![執行建立查詢](media/ingest-data-iot-hub/run-create-query.png)

1. 將下列命令複製到視窗中，然後選取 [執行]**** 以將傳入的 JSON 資料對應至資料表 (TestTable) 的資料行名稱與資料類型。

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>將 Azure 資料資源管理員表連線到 IoT 中心

現在,您將從 Azure 資料資源管理器連接到 IoT 中心。 此連線完成後,流入 iot 中心的資料將串流式傳輸到[您建立的目標表](#create-a-target-table-in-azure-data-explorer)。

1. 選擇工具列上的 **「通知」** 以驗證 IoT 中心部署是否成功。

1. 在建立的群組下,選擇 **「資料庫」** 然後選擇您建立的**testdb**的資料庫。
    
    ![選取測試資料庫](media/ingest-data-iot-hub/select-database.png)

1. 選取 [資料擷取]****，然後選取 [新增資料連線]****。 然後，在表單中填寫以下資訊。 選擇 **"完成時創建**"

    ![IoT 中心連接](media/ingest-data-iot-hub/iot-hub-connection.png)

    **資料來源**:

    **設定** | **欄位描述**
    |---|---|
    | 資料連線名稱 | 在 Azure 資料資源管理員中建立的連線的名稱
    | IoT 中樞 | IoT 中樞名稱 |
    | 共用存取原則 | 共用訪問策略的名稱。 必須具有讀取權限 |
    | 取用者群組 |  在 IoT 中心內建終結點中定義的使用者群組 |
    | 事件系統屬性 | [IoT 中心事件系統屬性](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages)。 新增系統屬性時,[建立](kusto/management/create-table-command.md)或[更新](kusto/management/alter-table-command.md)表架構和[映射](kusto/management/mappings.md)以包括所選屬性。 | | | 

    > [!NOTE]
    > 在[手動故障轉移](/azure/iot-hub/iot-hub-ha-dr#manual-failover)的情況下,必須重新創建數據連接。

    **目標表**:

    路由內嵌資料有兩個選項：靜態** 和動態**。 
    在本文中，您將使用靜態路由，您會指定資料表名稱、資料格式和對應。 因此，將 [我的資料包含路由資訊]**** 保留為未選取。

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | Table | *TestTable* | 您在**testdb**中建立的表。 |
    | 資料格式 | *JSON* | 支援的格式包括 Avro、CSV、JSON、多林傑森、PSV、SOHSV、SCSV、TSV、TSVE 和 TXT。 |
    | 資料行對應 | *TestMapping* | 在**testdb**中建立的[映射](kusto/management/mappings.md),它將傳入的 JSON 數據映射到列名稱和**testdb**的數據類型。 JSON、多林聯 JSON 和 AVRO 是必需的,其他格式可選。|
    | | |

    > [!NOTE]
    > * 選取 [我的資料包含路由資訊]**** 來使用動態路由，其中您的資料會包含必要的路由資訊，如[範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)註解中所示。 如果靜態和動態屬性都已設定，則動態屬性會覆寫靜態屬性。 
    > * 只有在創建數據連接後排隊的事件才會被引入。

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>組建測試的範例資料

模擬裝置應用程式會連線到 IoT 中樞上的裝置特定端點，並且傳送模擬的溫度和溼度遙測。

1. 從 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip 下載範例 C# 專案並將 ZIP 封存檔解壓縮。

1. 在本機終端機視窗中，瀏覽至範例 C# 專案的根資料夾。 然後瀏覽至 **iot-hub\Quickstarts\simulated-device** 資料夾。

1. 在您選擇的文字編輯器中開啟 **SimulatedDevice.cs** 檔案。

    將`s_connectionString`變數的值替換為從[註冊設備到 IoT 中心](#register-a-device-to-the-iot-hub)的設備連接字串。 然後將變更儲存到 **SimulatedDevice.cs** 檔案。

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

使用應用產生資料,您現在可以查看從 IoT 中心到群集中的表的數據流。

1. 在 Azure 門戶中,在 IoT 中心下,您可以看到應用運行時活動的激增。

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

    結果集:
    
    ![顯示引入的資料結果](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure 資料總管具有資料擷取的彙總 (批次處理) 原則，可將擷取程序最佳化。 默認情況下,該策略配置為 5 分鐘或 500 MB 的數據,因此可能會遇到延遲。 如需了解匯總選項，請參閱[批次原則](kusto/management/batchingpolicy.md)。 
    > * 配置表以支援流式處理並消除回應時間的延遲。 請參考[流式處理原則](kusto/management/streamingingestionpolicy.md)。 

## <a name="clean-up-resources"></a>清除資源

如果您不打算再次使用 IoT 中心,請清理**測試中心-rg,** 以避免產生成本。

1. 在 Azure 入口網站中選取靠左側的 [資源群組]****，然後選取您所建立的群組。  

    如果左側功能表已摺疊，請選取 ![[展開] 按鈕](media/ingest-data-event-hub/expand.png) 加以展開。

   ![選取要刪除的資源群組](media/ingest-data-event-hub/delete-resources-select.png)

1. 在 [test-resource-group]**** 下方，選取 [刪除資源群組]****。

1. 在新視窗中，輸入要刪除的資源群組名稱 (*test-hub-rg*)，然後選取 [刪除]****。

## <a name="next-steps"></a>後續步驟

* [Azure 資料資源管理員中的查詢資料](web-query-data.md)
