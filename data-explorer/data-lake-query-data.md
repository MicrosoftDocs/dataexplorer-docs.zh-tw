---
title: 使用 Azure 資料總管查詢 Azure Data Lake 中的資料
description: 瞭解如何使用 Azure 資料總管在 Azure Data Lake 中查詢資料。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: b7ca4a41bb15e9fb8a4f55a9d674b1536dddd5ae
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226392"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>使用 Azure 資料總管查詢 Azure Data Lake 中的資料

Azure Data Lake Storage 是可高度擴充且符合成本效益的 data Lake 解決方案，適用于海量資料分析。 它結合了高效能檔案系統的強大功能與大規模的經濟，協助您加快深入瞭解的時間。 Data Lake Storage Gen2 延伸 Azure Blob 儲存體的功能，並已針對分析工作負載優化。
 
Azure 資料總管與 Azure Blob 儲存體和 Azure Data Lake Storage （Gen1 和 Gen2）整合，提供對 Lake 中資料的快速、快取和索引存取。 您可以分析並查詢 lake 中的資料，而不需要事先內嵌到 Azure 資料總管。 您也可以同時在內嵌和 uningested 原生 lake data 之間進行查詢。  

> [!TIP]
> 最佳查詢效能會在 Azure 資料總管中內嵌資料。 查詢外部資料而不需要事先內嵌的功能，只應用於不常查詢的歷程記錄資料或資料。 [優化 lake 中的查詢效能](#optimize-your-query-performance)以獲得最佳結果。
 

## <a name="create-an-external-table"></a>建立外部資料表

 > [!NOTE]
 > 目前支援的儲存體帳戶為 Azure Blob 儲存體或 Azure Data Lake Storage （Gen1 和 Gen2）。

1. 使用 `.create external table` 命令在 Azure 資料總管中建立外部資料表。 其他外部資料表命令，例如 `.show` 、 `.drop` 和， `.alter` 會記錄在[外部資料表命令](kusto/management/externaltables.md)中。

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * 需要更細微的資料分割，以提高效能。 例如，針對具有每日分割區的外部資料表進行查詢，其效能會比具有每月分割資料表的查詢更好。
    > * 當您定義具有資料分割的外部資料表時，儲存結構應該是相同的。
例如，如果資料表是以 yyyy/MM/dd 格式（預設值）中的 DateTime 資料分割來定義，則 URI 儲存檔案路徑應該是*container1/yyyy/MM/dd/all_exported_blobs*。 
    > * 如果外部資料表是依日期時間資料行進行分割，請一律在查詢中包含已關閉範圍的時間篩選（例如，查詢，其 `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` 執行效能應高於此（已開啟的範圍）一 `ArchivedProducts | where Timestamp > ago(1h)` ）。 
    > * 所有[支援](ingestion-supported-formats.md)的內嵌格式都可以使用外部資料表來查詢。

1. 外部資料表會顯示在 Web UI 的左窗格中

    ![web UI 中的外部資料表](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>建立具有 json 格式的外部資料表

您可以使用 json 格式建立外部資料表。 如需詳細資訊，請參閱[外部資料表命令](kusto/management/externaltables.md)

1. 使用 `.create external table` 命令來建立名為*ExternalTableJson*的資料表：

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Json 格式需要第二個步驟來建立資料行的對應，如下所示。 在下列查詢中，建立名為*mappingName*的特定 json 對應：

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部資料表許可權
 
* 資料庫使用者可以建立外部資料表。 資料表建立者會自動成為資料表管理員。
* 叢集、資料庫或資料表管理員可以編輯現有的資料表。
* 任何資料庫使用者或讀取者都可以查詢外部資料表。
 
## <a name="query-an-external-table"></a>查詢外部資料表
 
若要查詢外部資料表，請使用 `external_table()` 函數，並提供資料表名稱做為函式引數。 查詢的其餘部分是標準的 Kusto 查詢語言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部資料表查詢目前不支援 Intellisense。

### <a name="query-an-external-table-with-json-format"></a>使用 json 格式查詢外部資料表

若要查詢具有 json 格式的外部資料表，請使用函式 `external_table()` ，並提供資料表名稱和對應名稱做為函式引數。 在下面的查詢中，如果未指定*mappingName* ，則會使用您先前建立的對應。

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>同時查詢外部和內嵌資料

您可以在相同的查詢中同時查詢外部資料表和內嵌資料表。 您 [`join`](kusto/query/joinoperator.md) 或 [`union`](kusto/query/unionoperator.md) 外部資料表包含 Azure 資料總管、SQL server 或其他來源的其他資料。 使用將 [`let( ) statement`](kusto/query/letstatement.md) 縮寫名稱指派給外部資料表參考。

在下列範例中，*產品*是內嵌的資料表，而*ArchivedProducts*是包含 Azure Data Lake Storage Gen2 中資料的外部資料表：

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>說明叢集中的查詢*TaxiRides*外部資料表

*TaxiRides*範例資料集包含[NYC 計程車和禮車委員會](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)的紐約市計程車資料。

### <a name="create-external-table-taxirides"></a>建立外部資料表*TaxiRides* 

> [!NOTE]
> 本節描述用*來在說明叢集中*建立*TaxiRides*外部資料表的查詢。 因為已建立此資料表，所以您可以略過此區段並執行[查詢*TaxiRides*外部資料表資料](#query-taxirides-external-table-data)。 

1. 下列查詢是用來在說明叢集中建立外部資料表*TaxiRides* 。 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. 產生的資料表是在說明叢*集中建立*的：

    ![TaxiRides 外部資料表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查詢*TaxiRides*外部資料表資料 

登入以 [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) 查詢*TaxiRides*外部資料表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查詢*TaxiRides*外部資料表而不進行資料分割

在外部資料表*TaxiRides*上[執行此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)，以在整個資料集中描繪一周的每一天的乘車。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查詢顯示一周最忙碌的日子。 由於資料並未分割，因此此查詢可能需要很長的時間才能傳回結果（最多數分鐘）。

![呈現非資料分割查詢](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>使用資料分割的查詢 TaxiRides 外部資料表 

在 [外部資料表] *TaxiRides*上[執行此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)，顯示2017年1月所使用的計程車封包類型（黃色或綠色）。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查詢使用資料分割，以優化查詢時間和效能。 查詢會篩選已分割的資料行（pickup_datetime），並在幾秒內傳回結果。

![呈現分割的查詢](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以撰寫要在外部資料表*TaxiRides*上執行的其他查詢，並深入瞭解資料。 

## <a name="optimize-your-query-performance"></a>優化您的查詢效能

使用下列查詢外部資料的最佳作法，將您在 lake 中的查詢效能優化。 
 
### <a name="data-format"></a>資料格式
 
自起，請使用單欄式格式進行分析查詢：
* 只可以讀取查詢的相關資料行。 
* 資料行編碼技術可以大幅減少資料大小。  
Azure 資料總管支援 Parquet 和 ORC 的單欄式格式。 建議採用 Parquet 格式，因為它是優化的執行。 
 
### <a name="azure-region"></a>Azure 區域 
 
確定外部資料與您的 Azure 資料總管叢集位於相同的 Azure 區域中。 這可降低成本和資料提取時間。
 
### <a name="file-size"></a>檔案大小
 
最佳檔案大小為每個檔案數百 Mb （最多 1 Gb）。 避免許多需要額外負荷的小型檔案，例如檔案列舉程式較慢，以及使用單欄式格式的有限用法。 請注意，檔案數目應大於 Azure 資料總管叢集中的 CPU 核心數目。 
 
### <a name="compression"></a>壓縮
 
使用壓縮來減少從遠端存放裝置提取的資料量。 針對 Parquet 格式，使用內部 Parquet 壓縮機制，以個別壓縮資料行群組，因此可讓您個別讀取它們。 若要驗證壓縮機制的使用方式，請檢查檔案的命名方式如下： " <filename> . gz. parquet" 或 " <filename> . snappy. parquet"，而不是 " <filename> . parquet. gz"）。 
 
### <a name="partitioning"></a>資料分割
 
使用「資料夾」分割區來組織您的資料，讓查詢略過無關的路徑。 規劃資料分割時，請考慮查詢中的檔案大小和一般篩選器，例如時間戳記或租使用者識別碼。
 
### <a name="vm-size"></a>VM 大小
 
選取具有更多核心的 VM Sku 和較高的網路輸送量（記憶體較不重要）。 如需詳細資訊，請參閱[為您的 Azure 資料總管叢集選取正確的 VM SKU](manage-cluster-choose-sku.md)。

## <a name="next-steps"></a>後續步驟

使用 Azure 資料總管查詢 Azure Data Lake 中的資料。 瞭解如何[撰寫查詢](write-queries.md)，並從您的資料衍生其他見解。
