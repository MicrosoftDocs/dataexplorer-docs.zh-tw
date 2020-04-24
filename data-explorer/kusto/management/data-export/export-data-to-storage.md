---
title: 將資料匯出到儲存 - Azure 資料資源管理員 |微軟文件
description: 本文介紹將資料匯出到 Azure 資料資源管理員中的儲存。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 12800955d1680a280aa4772db86d8b71e44e7089
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521568"
---
# <a name="export-data-to-storage"></a>匯出資料匯出到儲存

執行查詢並將第一個結果集寫入外部存儲,由[儲存連接字串](../../api/connection-strings/storage.md)指定。

**語法**

`.export`[`async``compressed` `to` ] 輸出*資料格式*
`(`*儲存連線字串*[`,` ]`)` `(` `=`*PropertyValue*`,` *PropertyName* // 屬性值 】 ... `with``)`• `<|` *查詢*

**引數**

* `async`:如果指定,則指示命令在非同步模式下運行。
  有關此模式下行為的更多詳細資訊,請參閱下文。

* `compressed`:如果指定,輸出存儲專案將壓縮為`.gz`檔案。 有關`compressionType`將鑲木地板檔壓縮為快速檔,請參閱。 

* *輸出資料格式*:指示命令編寫的存儲項目的數據格式。 支援的值是: `csv` `tsv``json`、、`parquet`和 。

* *儲存連線字串*:指定一個或多個[儲存連接字串](../../api/connection-strings/storage.md),指示將資料寫入哪個儲存。 (可以為可延伸寫入指定多個儲存連接字串。每個此類連接字串都必須指示寫入存儲時要使用的憑據。
  例如,寫入 Azure Blob 儲存時,憑據可以是儲存帳戶密鑰,也可以是具有讀取、寫入和清單 Blob 許可權的共享存取金鑰 (SAS)。

> [!NOTE]
> 強烈建議將數據匯出到與 Kusto 群集本身位於同一區域的存儲。 這包括匯出的數據,以便將其傳輸到其他區域中的另一個雲端服務。 寫入應在本地完成,而讀取可以遠程進行。

* *屬性名稱*/*屬性值*:零個或多個可選匯出屬性:

|屬性        |類型    |描述                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |正在寫入單個存儲專案的大小限制(在壓縮之前)。 允許的範圍為 100MB(預設)到 1GB。|
|`includeHeaders`|`string`|`csv`對於`tsv`輸出,控制列/標題的生成。 可以是(`none`預設;未發出標頭行)、(`all`將標題行發送到每個儲存工件中),也可以`firstFile`(僅將標頭行發射到第一個儲存工件中)。|
|`fileExtension` |`string`|指示儲存工件的「擴展」部分(例如,`.csv``.tsv`或 )。 如果使用壓縮,`.gz`也將追加壓縮。|
|`namePrefix`    |`string`|指示要添加到每個生成的儲存工件名稱的首碼。 如果未指定,將使用隨機前置字。       |
|`encoding`      |`string`|指示如何對文字進行編碼(`UTF8NoBOM`預設`UTF8BOM`) 或 。 |
|`compressionType`|`string`|指示要使用的壓縮類型。 可能的值為 `gzip` 或 `snappy`。 預設值為 `gzip`。 `snappy`可以(可選)用於`parquet`格式。 |
|`distribution`   |`string`  |分轉提示`single``per_node`(、 ) `per_shard` 如果值等於`single`,單個線程將寫入存儲。 否則,匯出將從並行執行查詢的所有節點寫入。 請參考[外掛程式運算子](../../query/evaluateoperator.md)。 預設為 `per_shard`。
|`distributed`   |`bool`  |禁用/啟用分散式匯出。 設置為 false 等`single`效於 分發提示。 預設值為 true。
|`persistDetails`|`bool`  |指示命令應保留其結果(請參閱`async`標誌)。 在非同步`true`中運行預設值,但如果調用方不需要結果,則可以關閉。 在同步執行`false`中預設為預設值,但也可以在同步執行中打開。 |
|`parquetRowGroupSize`|`int`  |僅當數據格式為鑲木地板時才相關。 控制匯出檔案中的列列大小。 默認行組大小為 100000 條記錄。|

**結果**

這些命令返回描述生成的儲存工件的表。
每個記錄描述單個專案,並包括專案存儲路徑及其保存的數據記錄數。

|Path|數位記錄|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**非同步模式**

如果指定`async`了標誌,則命令在非同步模式下執行。
在此模式下,命令會立即返回操作 ID,數據匯出在後台繼續,直到完成。 指令傳回的操作 ID 可用於透過以下指令追蹤其進度並最終其結果:

* [.顯示操作](../operations.md#show-operations):跟蹤進度。
* [.顯示操作詳細資訊](../operations.md#show-operation-details):獲取完成結果。

例如,成功完成後,可以使用以下功能檢索結果:

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**範例** 

在此範例中,Kusto 運行查詢,然後將查詢生成的第一個記錄集匯出到一個或多個壓縮 CSV blob。
列名稱標籤將添加為每個 Blob 的第一行。

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.windows.net/containerName;secretKey",
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

**已知問題**

*匯出指令期間的儲存錯誤*

預設情況下,匯出指令被分發,以便包含要同時匯出寫入的資料的所有[延伸區](../extents-overview.md)。 在大型匯出中,當此類擴展盤區的數量較高時,這可能導致存儲負載高,從而導致存儲限制或瞬態存儲錯誤。 在這種情況下,建議嘗試增加提供給匯出命令的存儲帳戶數(負載將在帳戶之間分配)和/或通過將分發提示設置為`per_node`(請參閱命令屬性)來減少併發性。 完全禁用分發也是可能的,但這可能會顯著影響命令性能。
 