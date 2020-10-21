---
title: 將資料匯出至外部資料表-Azure 資料總管
description: 本文說明如何將資料匯出至 Azure 資料總管中的外部資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ddce8e9a61fac4c089574f1e9f30d310304a3618
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342819"
---
# <a name="export-data-to-an-external-table"></a>將資料匯出至外部資料表

您可以藉由定義 [外部資料表](../external-table-commands.md) 並將資料匯出至資料來匯出資料。
資料表屬性是在 [建立外部資料表](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table)時指定的。 您不需要在 export 命令中內嵌資料表的屬性。 Export 命令會依名稱參考外部資料表。 匯出資料需要 [資料庫系統管理員許可權](../access-control/role-based-authorization.md)。

## <a name="syntax"></a>語法

`.export` [ `async` ] `to` `table` *ExternalTableName* <br>
[ `with` `(` *PropertyName* `=` *PropertyValue* `,` ... `)` ] <|*查詢*

## <a name="output"></a>輸出

|輸出參數 |類型 |說明
|---|---|---
|ExternalTableName  |String |外部資料表的名稱。
|Path|String|輸出路徑。
|NumRecords|String| 匯出至路徑的記錄數目。

## <a name="notes"></a>附註

* 匯出查詢輸出架構必須符合外部資料表的架構，包括分割區定義的所有資料行。 例如，如果資料表是依 *DateTime*進行分割，則查詢輸出架構必須有與 *TimestampColumnName*相符的時間戳記資料行。 此資料行名稱是在外部資料表分割定義中定義。

* 您無法使用 export 命令覆寫外部資料表屬性。
 例如，您無法將 Parquet 格式的資料匯出至資料格式為 CSV 的外部資料表。

* 以下是匯出命令的一部分支援的屬性。 如需詳細資訊，請參閱 [匯出至儲存體](export-data-to-storage.md) 一節： 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 如果外部資料表已分割，則會根據資料 [分割的外部資料表範例](#partitioned-external-table-example)中所見的資料分割定義，將匯出的構件寫入其各自的目錄。 
  * 如果資料分割值為 null/空白或為不正確目錄值，則根據目標儲存的定義，資料分割值會取代為預設值 `__DEFAULT_PARTITION__` 。 

### <a name="number-of-files"></a>檔案數目

每個分割區寫入的檔案數目取決於設定：
 * 如果外部資料表只包含 datetime 分割區，或完全沒有任何分割區，則針對每個分割區 (寫入的檔案數目（如果存在的話）) 應該類似叢集中的節點數目 (或更多，如果 `sizeLimit` 達到) 。 當匯出作業散發時，叢集中的所有節點會同時匯出。 若要停用散發，以便只有單一節點進行寫入，請將設定 `distributed` 為 false。 此程式會建立較少的檔案，但會降低匯出的效能。

* 如果外部資料表包含一個資料分割的資料行，則匯出的檔案數目應該是每個分割區的單一檔案 (或多個（如果 `sizeLimit` 達到) ）。 仍參與匯出 (作業的所有節點都會) 散發，但每個分割區會指派給特定的節點。 `distributed`如果設定為 false，則只會導致單一節點進行匯出，但行為會維持相同 (針對每個資料分割) 寫入的單一檔案。

## <a name="examples"></a>範例

### <a name="non-partitioned-external-table-example"></a>非資料分割的外部資料表範例

ExternalBlob 是非分割的外部資料表。 

```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>分割的外部資料表範例

PartitionedExternalBlob 是外部資料表，定義如下： 

```kusto
.create external table PartitionedExternalBlob (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

```kusto
.export to table PartitionedExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|ExternalBlob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

如果使用關鍵字) 以非同步方式執行命令 (`async` ，則可以使用 [ [顯示作業詳細資料](../operations.md#show-operation-details) ] 命令來取得輸出。