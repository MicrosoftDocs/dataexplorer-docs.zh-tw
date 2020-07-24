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
ms.openlocfilehash: 6b76f7a3ce61a0530d885de29c1a85d170431bb9
ms.sourcegitcommit: 4507466bdcc7dd07e6e2a68c0707b6226adc25af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87106446"
---
# <a name="export-data-to-storage"></a>將資料匯出至儲存體

執行查詢，並將第一個結果集寫入至[儲存體連接字串](../../api/connection-strings/storage.md)所指定的外部儲存體。

**語法**

`.export`[ `async` ] [ `compressed` ] `to` *OutputDataFormat* 
 `(` *StorageConnectionString* [ `,` ...] `)`[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|`*查詢*

**引數**

* `async`：如果指定，表示命令會以非同步模式執行。
  如需此模式中行為的詳細資訊，請參閱下文。

* `compressed`：如果指定，輸出儲存體成品會壓縮為檔案 `.gz` 。 如需將 `compressionType` parquet 檔案壓縮為 snappy 的詳細檔，請參閱。 

* *OutputDataFormat*：表示命令所寫入之儲存體成品的資料格式。 支援的值為： `csv` 、 `tsv` 、 `json` 和 `parquet` 。

* *StorageConnectionString*：指定一或多個[儲存體連接字串](../../api/connection-strings/storage.md)，指出要將資料寫入至哪個儲存體。 （可針對可調整的寫入指定一個以上的儲存體連接字串。）每個這類連接字串都必須指出要在寫入儲存體時使用的認證。
  例如，寫入 Azure Blob 儲存體時，認證可以是儲存體帳戶金鑰，或具有讀取、寫入和列出 blob 許可權的共用存取金鑰（SAS）。

> [!NOTE]
> 強烈建議將資料匯出至與 Kusto 叢集本身位於相同區域中的儲存體。 這包括匯出的資料，使其可以傳輸至其他區域中的其他雲端服務。 寫入應該在本機進行，而讀取則可以在遠端進行。

* *PropertyName* /*PropertyValue*：零或多個選擇性的匯出屬性：

|屬性        |類型    |說明                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |要寫入的單一儲存體成品大小限制（以位元組為單位）（壓縮前）。 允許的範圍是100MB （預設值）到1GB。|
|`includeHeaders`|`string`|若為 `csv` / `tsv` 輸出，則控制資料行標題的產生。 可以是 `none` （預設值; 未發出標頭行）、 `all` （在每個儲存成品中發出標頭行），或 `firstFile` （僅在第一個儲存體成品中發出標頭行）的其中一個。|
|`fileExtension` |`string`|指出儲存體成品的「延伸」部分（例如， `.csv` 或 `.tsv` ）。 如果使用壓縮，也 `.gz` 會附加。|
|`namePrefix`    |`string`|表示要新增至每個所產生之儲存體成品名稱的前置詞。 如果未指定，則會使用隨機前置詞。       |
|`encoding`      |`string`|表示如何編碼文字： `UTF8NoBOM` （預設）或 `UTF8BOM` 。 |
|`compressionType`|`string`|表示要使用的壓縮類型。 可能的值為 `gzip` 或 `snappy`。 預設為 `gzip`。 `snappy`可以（選擇性）用於 `parquet` 格式。 |
|`distribution`   |`string`  |散發提示（ `single` 、 `per_node` 、 `per_shard` ）。 如果值等於 `single` ，則單一線程會寫入至儲存體。 否則，匯出將會以平行方式執行查詢的所有節點寫入。 請參閱[評估外掛程式運算子](../../query/evaluateoperator.md)。 預設為 `per_shard`。
|`distributed`   |`bool`  |停用/啟用分散式匯出。 將設定為 false 相當於 `single` 散發提示。 預設值為 true。
|`persistDetails`|`bool`  |指出命令應該保存其結果（請參閱 `async` 旗標）。 `true`在非同步執行中預設為，但如果呼叫端不需要結果，則可以關閉。 同步執行中的預設值為 `false` ，但也可以在其中開啟。 |
|`parquetRowGroupSize`|`int`  |只有在 parquet 資料格式時才相關。 控制匯出檔案中的資料列群組大小。 預設資料列群組大小為100000記錄。|

**結果**

命令會傳回描述所產生之儲存體成品的資料表。
每一筆記錄都會描述單一成品，並包含成品的儲存路徑，以及它所保留的資料記錄數目。

|路徑|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**非同步模式**

如果 `async` 指定了旗標，命令就會以非同步模式執行。
在此模式中，命令會立即傳回作業識別碼，而資料匯出會在背景繼續進行，直到完成為止。 命令所傳回的作業識別碼，可以透過下列命令，用來追蹤其進度，最後是其結果：

* [。顯示作業](../operations.md#show-operations)：追蹤進度。
* [。顯示作業詳細資料](../operations.md#show-operation-details)：取得完成結果。

例如，在成功完成之後，您可以使用來取出結果：

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**範例** 

在此範例中，Kusto 會執行查詢，然後將查詢所產生的第一個記錄集匯出至一或多個壓縮的 CSV blob。
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

*匯出命令期間發生儲存體錯誤*

根據預設，匯出命令會散佈，讓包含資料的所有[範圍](../extents-overview.md)同時匯出至儲存體。 在大型匯出中，當這類範圍的數目很高時，這可能會導致儲存體負載過高，導致儲存節流或暫時性儲存體錯誤。 在這種情況下，建議您嘗試增加提供給 export 命令的儲存體帳戶數目（負載將會在帳戶之間散發）和/或，藉由將散發提示設定為來減少平行存取 `per_node` （請參閱命令屬性）。 也可以完全停用散發，但這可能會大幅影響命令效能。
 