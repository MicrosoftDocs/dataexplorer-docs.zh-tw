---
title: 使用 Azure 資料資源管理員在 Azure 資料湖中查詢資料
description: 瞭解如何使用 Azure 資料資源管理員查詢 Azure 數據湖中的數據。
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: aa1cdb647190c804c5dc18d1575a6030f166a845
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498574"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>使用 Azure 資料資源管理員在 Azure 資料湖中查詢資料

Azure 資料湖儲存是一種高度可擴展且經濟高效的數據湖解決方案,適用於大數據分析。 它將高性能文件系統的強大功能與大規模和經濟性相結合,説明您加快洞察速度。 數據存儲第 2 代擴展了 Azure Blob 儲存功能,並針對分析工作負荷進行了優化。
 
Azure 資料資源管理員與 Azure Blob 儲存和 Azure 資料湖儲存(第 1 代和 Gen2)整合,提供對湖中資料的快速、緩存和索引存取。 您可以分析和查詢湖中的數據,而無需事先引入 Azure 資料資源管理器。 您還可以同時查詢已引入和未引入的原生湖數據。  

> [!TIP]
> 最佳的查詢性能需要將數據引入 Azure 數據資源管理員。 無需事先引入即可查詢外部數據的功能應僅用於很少查詢的歷史數據或數據。 [優化湖中的查詢性能,](#optimize-your-query-performance)獲得最佳效果。
 

## <a name="create-an-external-table"></a>建立外部資料表

 > [!NOTE]
 > 當前支援的儲存帳戶是 Azure Blob 儲存或 Azure 資料湖儲存(第 1 代和第 2 代)。

1. 使用`.create external table`命令在 Azure 資料資源管理器中創建外部表。 其他外部表命令(如`.show``.drop`,`.alter`和 )在[外部表命令](kusto/management/externaltables.md)中記錄。

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
    > * 通過更精細的分區,預期性能會提高。 例如,對具有每日分區的外部表的查詢將比那些具有每月分區表的查詢具有更好的性能。
    > * 當您使用分區定義外部表時,存儲結構應相同。
例如,如果表使用日期時間分區以 yyyyy/MM/dd 格式(預設)定義,則 URI 儲存檔案路徑應為*容器1/yyyy/MM/dd/all_exported_blobs*。 
    > * 如果外部表按 datetime 列進行分區,則始終在查詢中包含關閉範圍的時間篩選器(例如,`ArchivedProducts | where Timestamp between (ago(1h) .. 10m)`查詢 - - 應比此(打開的`ArchivedProducts | where Timestamp > ago(1h)`範圍)1 - 執行得更好。 
    > * 可以使用外部表查詢所有[支援的引入格式](ingest-data-overview.md#supported-data-formats)。

1. 外部表在 Web UI 的左方窗格中可見

    ![Web UI 中的外部表](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>建立具有 json 格式的外部表

您可以建立具有 json 格式的外部表。 有關詳細資訊,請參閱[外部表命令](kusto/management/externaltables.md)

1. 使用`.create external table`指令建立名為 *「外部表Jon」的*表:

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
 
1. Json 格式需要第二步創建映射到列,如下所示。 在以下查詢中,建立名為*映射名稱*的特定 json 映射:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>外部表權限
 
* 資料庫用戶可以創建外部表。 表建立者自動成為表管理員。
* 群集、資料庫或表管理員可以編輯現有表。
* 任何資料庫用戶或讀取器都可以查詢外部表。
 
## <a name="query-an-external-table"></a>查詢外部表
 
要查詢外部表,請使用`external_table()`函數,並將表名稱作為函數參數提供。 查詢的其餘部分是標準的 Kusto 查詢語言。

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> 外部表查詢當前不支援 Intellisense。

### <a name="query-an-external-table-with-json-format"></a>使用 json 格式查詢外部表

要查詢具有 json 格式的外部表,`external_table()`請使用 函數,並提供表名稱和映射名稱作為函數參數。 在下面的查詢中,如果未指定*映射名稱*,將使用以前創建的映射。

```kusto
external_table('ExternalTableJson', 'mappingName')
```

## <a name="query-external-and-ingested-data-together"></a>查詢外部資料與引入資料

可以在同一查詢中查詢外部表和引入的數據表。 您[`join`](kusto/query/joinoperator.md)或[`union`](kusto/query/unionoperator.md)外部表具有來自 Azure 資料資源管理器、SQL 伺服器或其他源的其他資料。 使用[`let( ) statement`](kusto/query/letstatement.md)將速記名稱分配給外部表引用。

在下面的範例中,*產品*是一個引入的資料表,*存檔產品*是一個外部表,其中包含 Azure 資料湖儲存 Gen2 中的數據:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>查詢協助叢集的*遠端*表

*計程車樣本*數據集包含紐約市[計程車和豪華轎車委員會](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)提供的數據。

### <a name="create-external-table-taxirides"></a>建立外部表*計程車* 

> [!NOTE]
> 本節描述用於在*説明*群集中創建*TaxiRides*外部表的查詢。 由於此表已經建立,您可以跳過此部份並執行查詢[*TaxiRides*外部表資料](#query-taxirides-external-table-data)。 

1. 以下查詢用於在説明群集中創建外部表*TaxiRides。* 

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
1. 產生的表是在*說明*群組建立的:

    ![計程車外部表](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>查詢*計程車*外部表資料 

登錄以[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples)查詢*計程車外部*表。 

#### <a name="query-taxirides-external-table-without-partitioning"></a>查詢*計程車*外部表,無需分區

在外部表*TaxiRides*上運行[此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=),以描述整個數據集中每周每一天的騎行情況。 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

此查詢顯示一周中最繁忙的一天。 由於數據未分區,此查詢可能需要很長時間才能返回結果(最多幾分鐘)。

![呈現非分割區查詢](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>查詢帶分割區的外部表 

在外部表*TaxiRides*上運行[此查詢](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==),顯示 2017 年 1 月使用的計程車類型(黃色或綠色)。 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

此查詢使用分區,從而優化查詢時間和性能。 查詢篩選分區列 (pickup_datetime)並返回結果在幾秒鐘內。

![顯示分區查詢](media/data-lake-query-data/taxirides-with-partition.png)
  
您可以編寫其他查詢以在外部表*TaxiRides 上運行*,並瞭解有關資料的更多資訊。 

## <a name="optimize-your-query-performance"></a>優化查詢效能

使用以下最佳實務查詢外部資料,優化湖中的查詢性能。 
 
### <a name="data-format"></a>資料格式
 
使用柱格式進行分析查詢,因為:
* 只能讀取與查詢相關的列。 
* 列編碼技術可以顯著減小數據大小。  
Azure 資料資源管理員支援鑲木地板和 ORC 列格式。 由於優化的實施,建議採用鑲木地板格式。 
 
### <a name="azure-region"></a>Azure 區域 
 
確定外部數據與 Azure 資料資源管理器群集位於同一 Azure 區域中。 這減少了成本和數據提取時間。
 
### <a name="file-size"></a>檔案大小
 
最佳檔大小為每個檔數百 Mb(最多 1 Gb)。 避免許多需要不需要開銷的小檔,例如檔枚舉過程變慢和列格式使用有限。 請注意,檔數應大於 Azure 數據資源管理器群集中的 CPU 內核數。 
 
### <a name="compression"></a>壓縮
 
使用壓縮來減少從遠端存儲提取的數據量。 對於 Parquet 格式,請使用內部 Parquet 壓縮機制單獨壓縮列組,從而允許您單獨讀取它們。 要驗證壓縮機制的使用,請檢查檔案是否命名為如下<filename>:".gz.parquet"或".snappy.parquet",<filename>而不是".parquet.gz"。<filename> 
 
### <a name="partitioning"></a>資料分割
 
使用"資料夾"分區組織數據,使查詢能夠跳過不相關的路徑。 規劃分區時,請考慮查詢中的檔大小和常見篩選器,如時間戳或租戶 ID。
 
### <a name="vm-size"></a>VM 大小
 
選擇具有更多內核和更高網路輸送量的 VM SKU(記憶體不太重要)。 有關詳細資訊[,請參閱為 Azure 數據資源管理器群集選擇正確的 VM SKU。](manage-cluster-choose-sku.md)

## <a name="next-steps"></a>後續步驟

使用 Azure 資料資源管理員查詢 Azure 數據湖中的數據。 學習[編寫查詢](write-queries.md)並從數據中獲取其他見解。
