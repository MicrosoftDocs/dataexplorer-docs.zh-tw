---
title: 將資料匯出至外部資料表-Azure 資料總管 |Microsoft Docs
description: 本文說明如何將資料匯出至 Azure 資料總管中的外部資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 56150c480d0d5ecfd4d428e51f7bdb4b68e36b0c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617692"
---
# <a name="export-data-to-an-external-table"></a>將資料匯出至外部資料表

您可以藉由定義[外部資料表](../externaltables.md)並將資料匯出至其中，來匯出資料。
資料表屬性是在[建立外部資料表](../externaltables.md#create-or-alter-external-table)時指定的，因此，您不需要在 export 命令中內嵌資料表的屬性。 Export 命令會依名稱參考外部資料表。
匯出資料需要[資料庫系統管理員許可權](../access-control/role-based-authorization.md)。

> [!NOTE] 
> * 目前不支援匯出至具有`impersonate`連接字串的外部資料表。

**語法：**

`.export`[`async`] `to` `table` *ExternalTableName* <br>
[`with` `(` *PropertyName* PropertyName `=` * *PropertyValue`,`.。。`)`] <|*查詢*

**輸出**

|輸出參數 |類型 |描述
|---|---|---
|ExternalTableName  |String |外部資料表的名稱。
|Path|String|輸出路徑。
|NumRecords|String| 匯出到 path 的記錄數。

**注意：**
* 匯出查詢輸出架構必須符合外部資料表的架構，包括資料分割所定義的所有資料行。 例如，如果資料表依*日期時間*分割，則查詢輸出架構必須包含符合外部資料表分割定義中所定義之*TimestampColumnName*的時間戳記資料行。

* 您無法使用 export 命令來覆寫外部資料表屬性。
 例如，您無法將 Parquet 格式的資料匯出至資料格式為 CSV 的外部資料表。

* 下列屬性在 export 命令中受到支援。 如需詳細資訊，請參閱[匯出至儲存體](export-data-to-storage.md)一節： 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 如果外部資料表已分割，則會根據[範例](#partitioned-external-table-example)中所示的資料分割定義，將匯出的成品寫入其各自的目錄。 

* 每個分割區寫入的檔案數目取決於下列各項：
   * 如果外部資料表只包含 datetime 資料分割，或沒有任何資料分割，則寫入的檔案數目（針對每個分割區，如果存在）應會在叢集中的節點數目（如果`sizeLimit`已達到，則為更多）。 這是因為匯出作業已散發，因此叢集中的所有節點會同時匯出。 
   若要停用散發，讓只有單一節點執行寫入，請將`distributed`設定為 false。 這會建立較少的檔案，但會降低匯出效能。

   * 如果外部資料表包含資料分割的字串資料行，則匯出檔案的數目應該是每個分割區的單一檔案（如果`sizeLimit`達到，則為多個）。 所有的節點仍會參與匯出（作業會散發），但每個資料分割會指派給特定的節點。 在`distributed`此情況下，將設為 false，將只會導致單一節點執行匯出，但行為會維持不變（每個分割區所寫入的單一檔案）。

## <a name="examples"></a>範例

### <a name="non-partitioned-external-table-example"></a>非資料分割外部資料表範例

ExternalBlob 是非資料分割的外部資料表。 
```kusto
.export to table ExternalBlob <| T
```

|ExternalTableName|Path|NumRecords|
|---|---|---|
|ExternalBlob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>資料分割外部資料表範例

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

如果命令是以非同步方式執行（使用`async`關鍵字），則可以使用 [顯示作業[詳細資料](../operations.md#show-operation-details)] 命令來取得輸出。
