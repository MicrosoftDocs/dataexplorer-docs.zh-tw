---
title: Azure 資料資源管理員支援用於引入的數據格式。
description: 瞭解 Azure 資料資源管理員支援的各種數據和壓縮格式以進行引入。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 530fc02c58b719f29ef43b118fad1ce8f207337b
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81502539"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>Azure 資料資源管理員支援用於引入的資料格式

數據引入是將資料添加到表中並可在 Azure 資料資源管理器中查詢的過程。 對於除從查詢引入外的所有引入方法,數據必須採用受支援的格式之一。 下表列出了 Azure 資料資源管理員支援的數據引入的格式。

|[格式]   |分機   |描述|
|---------|------------|-----------|
|Avro     |`.avro`     |[Avro容器檔案](https://avro.apache.org/docs/current/)。 支援以下程式碼：`null`、`deflate` (`snappy` 目前不支援)。|
|阿帕帕夫羅|`.avro`    |[Avro](https://avro.apache.org/docs/current/)格式的實驗本機實現,支援[邏輯類型](https://avro.apache.org/docs/current/spec.html#Logical+Types)和`snappy`壓縮編解碼器。|
|CSV      |`.csv`      |具有逗號分隔值的文字檔 (`,`)。 請參考[RFC 4180:_逗號分隔值 (CSV) 檔的常見格式和 MIME 類型_](https://www.ietf.org/rfc/rfc4180.txt)。|
|JSON     |`.json`     |具有以 `\n` 或 `\r\n` 分隔的 JSON 文字檔。 請參閱 [JSON 程式碼行 (JSONL)](http://jsonlines.org/)。|
|多伊森|`.multijson`|文字檔，其中包含屬性包的 JSON 陣列 (每個屬性包都代表一筆記錄)，或任意多個以空白字元分隔的屬性包，`\n` 或 `\r\n`。 每個屬性包都可以散佈在多行上。 此格式優先於`JSON`,除非資料是非屬性包。|
|ORC      |`.orc`      |[Orc 檔](https://en.wikipedia.org/wiki/Apache_ORC)。|
|Parquet  |`.parquet`  |[Parquet 檔案](https://en.wikipedia.org/wiki/Apache_Parquet)。|
|PSV      |`.psv`      |具有分隔號分隔值 (<code>&#124;</code>) 的文字檔。|
|RAW      |`.raw`      |其整個內容為單一字串值的文字檔。|
|SCsv     |`.scsv`     |具有分號分隔值 (`;`) 的文字檔。|
|SOHsv    |`.sohsv`    |具有 SOH 分隔值的文字檔。 (SOH 是 ASCII 字碼指標 1；此格式是由 HDInsight 上的 Hive 使用)。|
|TSV      |`.tsv`      |具有定位字元分隔值 (`\t`) 的文字檔。|
|TSVE     |`.tsv`      |具有定位字元分隔值 (`\t`) 的文字檔。 反斜線字元 (`\`) 用於逸出。|
|TXT      |`.txt`      |具有以 `\n` 分隔之程式碼行的文字檔。 會跳過空白行。|

## <a name="supported-data-compression-formats"></a>支援的資料壓縮格式

可透過以下任一壓縮演演算法壓縮 Blob 和檔案:

|壓縮|分機|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

通過將副檔名追加到 blob 或檔的名稱來指示壓縮。

例如：
* `MyData.csv.zip`指示 blob 或格式化為 CSV 的檔案,使用 ZIP 壓縮(存檔或單個檔案)
* `MyData.csv.gz`指示使用 GZip 壓縮的 blob 或格式化為 CSV 的檔案

還支援不包含格式副檔名但僅包含壓縮(例如)的 Blob 或檔名。 在此情況下，您必須將檔案格式指定為擷取屬性 (因為無法加以推斷)。

> [!NOTE]
> 某些壓縮格式會追蹤原始檔案副檔名，作為壓縮資料流程的一部分。 通常會忽略此副檔名來判斷檔案格式。 如果無法從 (壓縮的) Blob 或檔案名稱判斷檔案格式，則必須透過 `format` 擷取屬性來指定。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[資料引入](/azure/data-explorer/ingest-data-overview)的更多
* 瞭解有關[Azure 資料資源管理員資料引入屬性](ingestion-properties.md)的更多
