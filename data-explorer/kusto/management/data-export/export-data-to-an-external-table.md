---
title: 將資料匯出到外部表 - Azure 資料資源管理員 |微軟文件
description: 本文介紹將資料匯出到 Azure 數據資源管理器中的外部表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: e26d1ae163b69f3a04c52538c245ea446dc11199
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521636"
---
# <a name="export-data-to-an-external-table"></a>匯出資料匯出到外部表

您可以通過定義[外部表](../externaltables.md)並將資料匯出到外部表來匯出資料。
創建[外部表](../externaltables.md#create-or-alter-external-table)時指定表屬性,因此,不需要在匯出命令中嵌入表的屬性。 匯出命令按名稱引用外部表。
匯出資料需要[資料庫管理員權限](../access-control/role-based-authorization.md)。

> [!NOTE] 
> * 當前不支援使用`impersonate`連接字串匯出到外部表。

**語法：**

`.export``async` `to` 【 `table` %*外部表名稱* <br>
•`with` `(`*屬性名稱*`=`*屬性值*`,`...`)`[ <]*查詢*

**輸出:**

|輸出參數 |類型 |描述
|---|---|---
|外部表名稱  |String |外部表的名稱。
|Path|String|輸出路徑。
|數位記錄|String| 匯出到路徑的記錄數。

**注意：**
* 匯出查詢輸出架構必須與外部表的架構匹配,包括分區定義的所有列。 例如,如果表按*DateTime*分區,則查詢輸出架構必須包含與外部表分區定義中定義的*時間戳列名稱*匹配的時間戳列。

* 無法使用匯出命令重寫外部表屬性。
 例如,不能將鑲木地板格式的數據匯出到數據格式為 CSV 的外部表。

* 作為匯出命令的一部分,支援以下屬性。 有關詳細資訊[,請參閱匯出到存儲](export-data-to-storage.md)部分: 
   * `sizeLimit`, `parquetRowGroupSize`, `distributed`.

* 如果外部表被分區,則匯出的專案將寫入其各自的目錄,根據[示例](#partitioned-external-table-example)中所示的分區定義。 

* 每個分割區寫入的檔案數取決於以下內容:
   * 如果外部表僅包含日期時間分區,或者根本沒有分區 - 寫入的檔數(對於每個分區,如果存在)應圍繞群集中的節點數(如果`sizeLimit`達到) 或更多。 這是因為匯出操作是分散式的,因此群集中的所有節點同時匯出。 
   禁用分發,以便只有一個節點執行寫入,將寫入`distributed`設置為 false。 這將創建較少的檔,但會降低匯出性能。

   * 如果外部表包含按字串列劃分的分區,則匯出的檔數應為每個分區的單個檔(如果達到,`sizeLimit`則更多)。 所有節點仍參與匯出(操作是分散式的),但每個分區都分配給特定節點。 在這種情況下`distributed`,設置為 false 將導致只有一個節點執行匯出,但行為將保持不變(每個分區寫入的單個檔)。

## <a name="examples"></a>範例

### <a name="non-partitioned-external-table-example"></a>非分割區外部表示例

外部 Blob 是非分區的外部表。 
```kusto
.export to table ExternalBlob <| T
```

|外部表名稱|Path|數位記錄|
|---|---|---|
|外部 Blob|http://storage1.blob.core.windows.net/externaltable1cont1/1_58017c550b384c0db0fea61a8661333e.csv|10|

### <a name="partitioned-external-table-example"></a>分割區外部表示例

分區外部Blob 是一個外部表,定義如下: 

```
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

```
.export to table PartitionedExternalBlob <| T
```

|外部表名稱|Path|數位記錄|
|---|---|---|
|外部 Blob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer1/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_1_58017c550b384c0db0fea61a8661333e.csv|10|
|外部 Blob|http://storageaccount.blob.core.windows.net/container1/CustomerName=customer2/2019/01/01/fa36f35c-c064-414d-b8e2-e75cf157ec35_2_b785beec2c004d93b7cd531208424dc9.csv|10|

如果命令是非同步執行的(透過使用`async`關鍵字),則輸出可以使用 show[操作詳細資訊](../operations.md#show-operation-details)命令。