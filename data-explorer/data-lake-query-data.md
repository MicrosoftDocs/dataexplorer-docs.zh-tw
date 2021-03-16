---
title: 使用 Azure 資料總管查詢 Azure Data Lake 中的資料
description: 了解如何使用 Azure 資料總管查詢 Azure Data Lake 中的資料。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/17/2020
ms.localizationpriority: high
ms.openlocfilehash: df9c20fc19a2d3c33d440df72ee33b1449b5c60b
ms.sourcegitcommit: 600c3430c00eb62d52540fe08dab386a860193cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2021
ms.locfileid: "102472156"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>使用 Azure 資料總管查詢 Azure Data Lake 中的資料

Azure Data Lake Storage 是有高度調整性並合乎成本效益的資料湖解決方案，適用於巨量資料分析。 其結合了高效能檔案系統的強大功能與大規模的經濟，協助您減少深入解析的時間。 Data Lake Storage Gen2 擴充 Azure Blob 儲存體功能，並已針對分析工作負載最佳化。
 
Azure 資料總管可與 Azure Blob 儲存體和 Azure Data Lake Storage (Gen1 和 Gen2) 整合，以對外部儲存體中儲存的資料提供快速、快取和檢索存取。 您不需事先在 Azure 資料總管中進行內嵌，即可分析和查詢資料。 您也可以同時查詢已內嵌和未內嵌的外部資料。  

> [!TIP]
> 必須在 Azure 資料總管中進行資料內嵌才能達到最佳查詢效能。 查詢外部資料而不需事先內嵌的功能，只能使用於歷史資料或很少查詢的資料。 [最佳化您的外部資料查詢效能](#optimize-your-query-performance)以獲得最佳結果。
 
## <a name="create-an-external-table"></a>建立外部資料表

假設您有許多 CSV 檔案包含倉儲中所儲存產品的歷程記錄資訊，而您想要進行快速分析，以找出去年最熱門的五項產品。 在此範例中，CSV 檔案如下所示：

| 時間戳記 | ProductId   | ProductDescription |
|-----------|-------------|--------------------|
| 2019-01-01 11:21:00 | TO6050 | 3.5 吋 DS/HD 磁碟片 |
| 2019-01-01 11:30:55 | YDX1   | Yamaha DX1 合成器  |
| ...                 | ...    | ...                     |

這些檔案會儲存在 Azure Blob 儲存體 `mycompanystorage` 中名為 `archivedproducts` 的容器下 (依日期分割)：

```
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00001-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00002-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00003-cd5fad16-a45e-4f8c-a2d0-5ea5de2f4e02.csv.gz
https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ffc72d50-ff98-423c-913b-75482ba9ec86.csv.gz
...
```

若要直接在這些 CSV 檔案上執行 KQL 查詢，請使用 `.create external table` 命令在 Azure 資料總管中定義外部資料表。 如需外部資料表 create 命令選項的詳細資訊，請參閱[外部資料表命令](kusto/management/external-tables-azurestorage-azuredatalake.md)。

```Kusto
.create external table ArchivedProducts(Timestamp:datetime, ProductId:string, ProductDescription:string)   
kind=blob            
partition by (Date:datetime = bin(Timestamp, 1d))   
dataformat=csv   
(   
  h@'https://mycompanystorage.blob.core.windows.net/archivedproducts;StorageSecretKey'
)    
```

外部資料表現在會顯示在 Web UI 的左窗格中：

:::image type="content" source="media/data-lake-query-data/external-tables-web-ui.png" alt-text="Web UI 中的外部資料表":::
 
### <a name="external-table-permissions"></a>外部資料表權限
 
* 資料庫使用者可以建立外部資料表。 資料表建立者會自動成為資料表管理員。
* 叢集、資料庫或資料表管理員可以編輯現有的資料表。
* 任何資料庫使用者或讀取者都可以查詢外部資料表。

## <a name="querying-an-external-table"></a>查詢外部資料表
 
定義外部資料表之後，`external_table()` 函式即可用於參考該資料表。 查詢的其餘部分是標準 Kusto 查詢語言。

```Kusto
external_table("ArchivedProducts")   
| where Timestamp > ago(365d)   
| summarize Count=count() by ProductId,   
| top 5 by Count
```

## <a name="querying-external-and-ingested-data-together"></a>同時查詢外部和內嵌的資料

您可以在相同的查詢中同時查詢外部資料表和內嵌資料表。 您可以 [`join`](kusto/query/joinoperator.md) 或 [`union`](kusto/query/unionoperator.md) 外部資料表與來自 Azure 資料總管、SQL 伺服器或其他來源的其他資料。 使用 [`let( ) statement`](kusto/query/letstatement.md)，將縮寫名稱指派給外部資料表參考。

在下列範例中，*Products* 是內嵌資料表，而 *ArchivedProducts* 是我們先前定義的外部資料表：

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);   
let T = Products; //T is an internal table   
T1 | join T on ProductId | take 10
```

## <a name="querying-hierarchical-data-formats"></a>查詢階層式資料格式

Azure 資料總管允許查詢階層式格式，例如 `JSON`、`Parquet`、`Avro` 和 `ORC`。 若要將階層式資料結構描述對應至外部資料表結構描述 (如果不同的話)，請使用[外部資料表對應命令](kusto/management/external-tables-azurestorage-azuredatalake.md#create-external-table-mapping)。 例如，如果您想要使用下列格式查詢 JSON 記錄檔：

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

外部資料表定義如下所示：

```kusto
.create external table ApiCalls(Timestamp: datetime, TenantId: guid, MethodName: string)
kind=blob
dataformat=multijson
( 
   h@'https://storageaccount.blob.core.windows.net/container1;StorageSecretKey'
)
```

定義 JSON 對應，以將資料欄位對應至外部資料表定義欄位：

```kusto
.create external table ApiCalls json mapping 'MyMapping' '[{"Column":"Timestamp","Properties":{"Path":"$.timestamp"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]'
```

當您查詢外部資料表時，將會叫用對應，而相關資料將會對應到外部資料表資料行：

```kusto
external_table('ApiCalls') | take 10
```

如需對應語法的詳細資訊，請參閱[資料對應](kusto/management/mappings.md)。

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查詢說明叢集中的 *TaxiRides* 外部資料表

使用名為 *help* 的測試叢集 來試用不同的 Azure 資料總管功能。 *help* 叢集包含 [紐約市計程車資料集](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)的外部資料表定義，該資料集包含數十億筆計程車搭乘。

### <a name="create-external-table-taxirides"></a>建立外部資料表 *TaxiRides* 

本節說明用於在 *help* 叢集中建立 *TaxiRides* 外部資料表的查詢。 因為已建立此資料表，所以您可略過本節並直接移至 [查詢 *TaxiRides* 外部資料表資料](#query-taxirides-external-table-data)。

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
partition by (Date:datetime = bin(pickup_datetime, 1d))   
dataformat=csv
( 
    h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

您可藉由查看 Web UI 的左窗格，尋找所建立的 **TaxiRides** 資料表：

:::image type="content" source="media/data-lake-query-data/taxirides-external-table.png" alt-text="計程車搭乘外部資料表":::

### <a name="query-taxirides-external-table-data"></a>查詢 *TaxiRides* 外部資料表資料 

登入 [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查詢未分割的 *TaxiRides* 外部資料表

在 *TaxiRides* 外部資料表上 [執行此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=)，以顯示整個資料集內一周每一天的搭乘。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查詢會顯示一周最忙碌的一天。 由於資料並未分割，因此查詢最多可能需要數分鐘的時間來傳回結果。

:::image type="content" source="media/data-lake-query-data/taxirides-no-partition.png" alt-text="呈現未分割的查詢":::

#### <a name="query-taxirides-external-table-with-partitioning"></a>查詢已分割的 TaxiRides 外部資料表 

在外部資料表 *TaxiRides* 上 [執行此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==)，以顯示 2017 年 1 月所使用的計程車類型(黃色或綠色)。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查詢使用資料分割，以最佳化查詢時間和效能。 查詢會依據已分割的資料行 (pickup_datetime) 篩選並在幾秒內傳回結果。

:::image type="content" source="media/data-lake-query-data/taxirides-with-partition.png" alt-text="呈現已分割的查詢":::
  
您可以撰寫要在外部資料表 *TaxiRides* 上執行的其他查詢並深入了解資料。 

## <a name="optimize-your-query-performance"></a>最佳化您的查詢效能

使用下列可供查詢外部資料的最佳做法，將您在資料湖中的查詢效能最佳化。 
 
### <a name="data-format"></a>資料格式
 
* 針對分析查詢使用單欄式格式，原因如下：
    * 只可以讀取查詢的相關資料行。 
    * 資料行編碼技術可以大幅縮小資料大小。  
* Azure 資料總管支援 Parquet 和 ORC 單欄式格式。 建議使用 Parquet 格式，因為其實作已最佳化。 
 
### <a name="azure-region"></a>Azure 區域 
 
檢查外部資料是否與您的 Azure 資料總管叢集位於相同的 Azure 區域中。 此設定可降低成本和資料擷取時間。
 
### <a name="file-size"></a>檔案大小
 
最佳檔案大小為每個檔案數百 Mb (最多 1 GB)。 避免許多需要額外負荷的小型檔案，例如較慢的檔案列舉程序和有限的單欄式格式使用。 檔案數目應大於 Azure 資料總管叢集中的 CPU 核心數目。 
 
### <a name="compression"></a>壓縮
 
使用壓縮來減少從遠端儲存體擷取的資料量。 針對 Parquet 格式，使用可個別壓縮資料行群組的內部 Parquet 壓縮機制，讓您能個別進行讀取。 若要驗證壓縮機制的使用，請檢查檔案的命名方式是否如下： *&lt;filename&gt;.gz.parquet* 或 *&lt;filename&gt;.snappy.parquet*，而不是 *&lt;filename&gt;.parquet.gz*。 
 
### <a name="partitioning"></a>資料分割
 
使用可讓查詢略過無關路徑的「資料夾」分割區來組織您的資料。 在規劃資料分割時，請考慮查詢中的檔案大小和一般篩選條件，例如時間戳記或租用戶識別碼。
 
### <a name="vm-size"></a>VM 大小
 
選取具有更多核心和較高網路輸送量的 VM SKU (記憶體比較不重要)。 如需詳細資訊，請參閱[為您的 Azure 資料總管叢集選取正確的 VM SKU](manage-cluster-choose-sku.md)。

## <a name="next-steps"></a>後續步驟

* 使用 Azure 資料總管查詢 Azure Data Lake 中的資料。 了解如何[撰寫查詢](write-queries.md)並從您的資料衍生出其他見解。
