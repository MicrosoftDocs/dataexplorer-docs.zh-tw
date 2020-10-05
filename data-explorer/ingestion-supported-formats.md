---
title: Azure 資料總管針對內嵌所支援的資料格式。
description: 瞭解 Azure 資料總管針對內嵌所支援的各種資料和壓縮格式。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 4f7e61755b12c84fc49373a12edc0b507aee9bf4
ms.sourcegitcommit: 2764e739b4ad51398f4f0d3a9742d7168c4f5fd7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712046"
---
# <a name="data-formats-supported-by-azure-data-explorer-for-ingestion"></a>適用于內嵌的 Azure 資料總管所支援的資料格式

資料內嵌是指將資料新增至資料表並可在 Azure 資料總管中進行查詢的程式。 對於內嵌自查詢以外的所有內嵌方法，資料必須是其中一種支援的格式。 下表列出並說明 Azure 資料總管針對資料內嵌所支援的格式。

|格式   |副檔名   |描述|
|---------|------------|-----------|
|ApacheAvro|`.avro`    |[Avro](https://avro.apache.org/docs/current/)格式，可支援[邏輯類型](https://avro.apache.org/docs/current/spec.html#Logical+Types)和 `snappy` 壓縮編解碼器。|
|Avro     |`.avro`     |[Avro 容器](https://avro.apache.org/docs/current/)檔案的舊版執行。 以下是支援的程式碼 `null` ： `deflate` (的 `snappy` 使用 `apacheavro` 檔案格式) 。|
|CSV      |`.csv`      |具有逗號分隔值的文字檔 (`,`)。 請參閱 [RFC 4180： _逗點分隔值的一般格式和 MIME 類型 (CSV) _](https://www.ietf.org/rfc/rfc4180.txt)檔。|
|JSON     |`.json`     |具有以 `\n` 或 `\r\n` 分隔的 JSON 文字檔。 請參閱 [JSON 程式碼行 (JSONL)](http://jsonlines.org/)。|
|MultiJSON|`.multijson`|文字檔，其中包含屬性包的 JSON 陣列 (每個屬性包都代表一筆記錄)，或任意多個以空白字元分隔的屬性包，`\n` 或 `\r\n`。 每個屬性包都可以散佈在多行上。 `JSON`除非資料是非屬性包，否則偏好此格式。|
|ORC      |`.orc`      |[Orc 檔](https://en.wikipedia.org/wiki/Apache_ORC)。|
|Parquet  |`.parquet`  |[Parquet 檔案](https://en.wikipedia.org/wiki/Apache_Parquet)。|
|PSV      |`.psv`      |具有分隔號分隔值 (<code>&#124;</code>) 的文字檔。|
|RAW      |`.raw`      |其整個內容為單一字串值的文字檔。|
|SCsv     |`.scsv`     |具有分號分隔值 (`;`) 的文字檔。|
|SOHsv    |`.sohsv`    |具有 SOH 分隔值的文字檔。 (SOH 是 ASCII 字碼指標 1；此格式是由 HDInsight 上的 Hive 使用)。|
|TSV      |`.tsv`      |具有定位字元分隔值 (`\t`) 的文字檔。|
|TSVE     |`.tsv`      |具有定位字元分隔值 (`\t`) 的文字檔。 反斜線字元 (`\`) 用於逸出。|
|TXT      |`.txt`      |具有以 `\n` 分隔之程式碼行的文字檔。 會跳過空白行。|
|W3CLOGFILE |`.log`    |W3C standardised 的[Web 記錄](https://www.w3.org/TR/WD-logfile.html)檔格式。|

## <a name="supported-data-compression-formats"></a>支援的資料壓縮格式

Blob 和檔案可透過下列任何壓縮演算法來壓縮：

|壓縮|副檔名|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

藉由將擴充功能附加至 blob 或檔案的名稱，來表示壓縮。

例如：
* `MyData.csv.zip` 指出格式化為 CSV、以 ZIP 壓縮 (封存或單一檔案的 blob 或檔) 
* `MyData.json.gz` 指出格式化為 JSON 並以 GZip 壓縮的 blob 或檔案。

Blob 或檔案名不包含格式延伸，但只是壓縮 (例如， `MyData.zip` 也支援) 。 在此情況下，您必須將檔案格式指定為擷取屬性 (因為無法加以推斷)。

> [!NOTE]
> 某些壓縮格式會追蹤原始檔案副檔名，作為壓縮資料流程的一部分。 通常會忽略此副檔名來判斷檔案格式。 如果無法從 (壓縮的) Blob 或檔案名稱判斷檔案格式，則必須透過 `format` 擷取屬性來指定。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[資料](ingest-data-overview.md)內嵌
* 深入瞭解 [Azure 資料總管資料內嵌屬性](ingestion-properties.md)
