---
title: 將資料匯出至儲存體-Azure 資料總管 |Microsoft Docs
description: 本文說明如何將資料匯出至 Azure 資料總管中的儲存體。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: b470d017937ed6f2687016ab8a7cf53fed7b51ab
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614486"
---
# <a name="export-data-to-storage"></a>將資料匯出至儲存體

執行查詢，並將第一個結果集寫入至 [儲存體連接字串](../../api/connection-strings/storage.md)所指定的外部儲存體。

**語法**

`.export`[ `async` ] [ `compressed` ] `to` *OutputDataFormat* 
 `(` *StorageConnectionString* [ `,` ...] `)`[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*查詢*

**引數**

* `async`：如果已指定，則表示命令以非同步模式執行。
  如需此模式中行為的詳細資訊，請參閱下文。

* `compressed`：如果有指定，輸出儲存成品會壓縮為檔案 `.gz` 。 如需將 `compressionType` parquet 檔案壓縮為 snappy，請參閱。 

* *OutputDataFormat*：指出命令所寫入之儲存體成品的資料格式。 支援的值為： `csv` 、 `tsv` 、 `json` 和 `parquet` 。

* *StorageConnectionString*：指定一或多個 [儲存體連接字串](../../api/connection-strings/storage.md) ，以指出要寫入資料的儲存體。  (可以為可調整的寫入指定一個以上的儲存體連接字串。 ) 這類連接字串必須指出寫入儲存體時要使用的認證。
  例如，寫入至 Azure Blob 儲存體時，認證可以是儲存體帳戶金鑰，也可以是共用存取金鑰 (SAS) 具有讀取、寫入和列出 blob 的許可權。

> [!NOTE]
> 強烈建議您將資料匯出至與 Kusto 叢集本身共置於相同區域的儲存體。 這包括匯出的資料，因此可以轉移到其他區域中的另一個雲端服務。 寫入應該在本機進行，而讀取則可以從遠端進行。

* *PropertyName* /*PropertyValue*：零或多個選用的匯出屬性：

|屬性        |類型    |說明                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |在壓縮) 之前， (寫入單一儲存體成品的大小限制（以位元組為單位）。 允許的範圍為 100MB (預設) 為1GB。|
|`includeHeaders`|`string`|針對 `csv` / `tsv` 輸出，會控制資料行標頭的產生。 可以是 `none` (預設值中的其中一個; 不會發出) 的標頭行、 `all` (將標頭行發出至每個儲存成品) ，或 `firstFile` (只) 將標頭行發出至第一個儲存體成品。|
|`fileExtension` |`string`|表示儲存體成品的 "extension" 部分 (例如， `.csv` 或 `.tsv`) 。 如果使用壓縮，則 `.gz` 也會附加。|
|`namePrefix`    |`string`|表示要加入每個產生的儲存體成品名稱的前置詞。 如果未指定，則會使用隨機前置詞。       |
|`encoding`      |`string`|指出如何編碼文字： `UTF8NoBOM` (預設) 或 `UTF8BOM` 。 |
|`compressionType`|`string`|指出要使用的壓縮類型。 可能的值為 `gzip` 或 `snappy`。 預設為 `gzip`。 `snappy` (可以選擇性地) 用於 `parquet` 格式。 |
|`distribution`   |`string`  |散發提示 (`single` 、 `per_node` 、 `per_shard`) 。 如果值等於 `single` ，單一執行緒將會寫入至儲存體。 否則，匯出將會以平行方式執行查詢的所有節點寫入。 請參閱 [評估外掛程式運算子](../../query/evaluateoperator.md)。 預設值為 `per_shard`。
|`distributed`   |`bool`  |停用/啟用分散式匯出。 將設定為 false 相當於 `single` 散發提示。 預設值為 true。
|`persistDetails`|`bool`  |指出命令應該保存其結果 (請參閱 `async` 旗標) 。 `true`在非同步執行中預設為，但如果呼叫端不需要) 的結果，則可以關閉。 預設為 `false` 同步執行，但也可以在其中開啟。 |
|`parquetRowGroupSize`|`int`  |只有在 parquet 資料格式時才相關。 控制匯出檔案中的資料列群組大小。 預設資料列群組大小為100000筆記錄。|

**結果**

這些命令會傳回描述所產生之儲存體成品的表格。
每一筆記錄都會描述單一成品，並包括成品的儲存體路徑，以及它所保留的資料記錄數目。

|路徑|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**非同步模式**

如果 `async` 指定了旗標，命令就會以非同步模式執行。
在此模式中，命令會立即以作業識別碼傳回，而且資料匯出會在背景繼續進行，直到完成為止。 命令所傳回的作業識別碼可以用來追蹤其進度，以及透過下列命令的最終結果：

* [。顯示作業](../operations.md#show-operations)：追蹤進度。
* [。顯示作業詳細資料](../operations.md#show-operation-details)：取得完成結果。

例如，在成功完成後，您可以使用下列內容抓取結果：

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**範例** 

在此範例中，Kusto 會執行查詢，然後將查詢所產生的第一個記錄集匯出至一個或多個壓縮的 CSV blob。
資料行名稱標籤會新增為每個 blob 的第一個資料列。

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

#### <a name="known-issues"></a>已知問題

**匯出命令期間發生失敗**

* 匯出命令可能會在執行期間暫時失敗。 當匯出命令失敗時，將不會刪除已寫入儲存區的成品。 這些成品將會保留在儲存體中。 如果命令失敗，即使已寫入某些成品，也會假設匯出不完整。 追蹤命令完成和成功完成時匯出之成品的最佳方式，是使用 [。顯示作業](../operations.md#show-operations) 和 [. 顯示作業詳細資料](../operations.md#show-operation-details) 命令。

* 依預設，會將匯出命令分散，使包含要匯出之資料的所有 [範圍](../extents-overview.md) 同時寫入至儲存體。 在大型匯出中，當這類範圍的數目很高時，這可能會導致儲存體負載過高，而導致儲存體節流或暫時性儲存錯誤。 在這種情況下，建議您嘗試增加提供給 export 命令的儲存體帳戶數目 (負載會分散到帳戶) 和/或藉由將散發提示設定為 `per_node` (請參閱命令屬性) ，以減少平行存取。 也可以完全停用散發，但這可能會對命令效能造成顯著的影響。
