---
title: Kusto 內嵌至命令 (從儲存體) 提取資料-Azure 資料總管
description: 本文說明如何將內嵌至命令 (從 Azure 資料總管中的儲存體) 提取資料。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 90158c066c724f7d05e9c5e91333874ad572e81b
ms.sourcegitcommit: 53679e57e0fcb7ec46beaba7cc812bd991da6cc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87984465"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>將內嵌至命令 (從儲存體提取資料) 

命令會藉 `.ingest into` 由「提取」一或多個雲端儲存體檔案中的資料，將資料內嵌到資料表中。
例如，命令可以從 Azure Blob 儲存體取出 1000 CSV 格式的 blob、剖析它們，並將它們一起內嵌到單一目標資料表中。
資料會附加至資料表，而不會影響現有的記錄，而且不會修改資料表的架構。

**語法**

`.ingest`[ `async` ] `into` `table` *TableName* *SourceDataLocator* [ `with` `(` *IngestionPropertyName* `=` *IngestionPropertyValue* [ `,` ...] `)` ]

**引數**

* `async`：如果指定，此命令會立即傳回，並繼續在背景中進行內嵌。 此命令的結果將會包含一個 `OperationId` 值，之後可以使用此 `.show operation` 命令來抓取內嵌完成狀態和結果。
  
* *TableName*：要內嵌資料的資料表名稱。
  資料表名稱一律會相對於內容中的資料庫，而且如果未提供任何架構對應物件，其架構就是將會假設用於資料的架構。

* *SourceDataLocator*：類型的常值 `string` ，或是以和字元括住的這類常值清單（以逗號分隔） `(` `)` ，代表[儲存體連接字串](../../api/connection-strings/storage.md)。 Kusto 會使用 URI 格式來描述包含要提取之資料的儲存體檔案。 
  * 單一連接字串必須參考儲存體帳戶所主控的單一檔案。 
  * 藉由指定以逗號分隔的多個連接字串，或從[外部資料表](../../query/schema-entities/externaltables.md)的[查詢中內嵌](ingest-from-query.md)，即可進行內嵌多個檔案。

> [!NOTE]
> 強烈建議您針對包含實際認證的*SourceDataPointer* ，使用[模糊的字串常](../../query/scalar-data-types/string.md#obfuscated-string-literals)值。
> 服務會確定在其內部追蹤、錯誤訊息等中清除認證。

* *IngestionPropertyName*， *IngestionPropertyValue*：會影響內嵌進程的任意數目的內嵌[屬性](../../../ingestion-properties.md)。

**結果**

命令的結果是一個資料表，其中包含多筆記錄，因為命令所產生的資料分區 ( 「範圍」 ) 。
如果未產生任何資料分區，則會傳回具有空白 (零值) 範圍識別碼的單一記錄。

|名稱       |類型      |描述                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|ExtentId   |`guid`    |命令所產生之資料分區的唯一識別碼。|
|ItemLoaded |`string`  |與此記錄相關的一或多個儲存體檔案。             |
|Duration   |`timespan`|執行內嵌所花費的時間。                                     |
|HasErrors  |`bool`    |此記錄是否代表內嵌失敗。                |
|OperationId|`guid`    |代表作業的唯一識別碼。 可以搭配 `.show operation` 命令使用。|

**備註**

此命令不會修改要內嵌至之資料表的架構。
如有必要，資料會在內嵌期間「強制轉型」到此架構中，而不是 (額外資料行略過的另一種方式，而將遺漏資料行視為 null 值) 。

**範例**

下一個範例會指示引擎從 Azure Blob 儲存體讀取兩個 blob 做為 CSV 檔案，並將其內容內嵌到資料表中 `T` 。 `...`代表 Azure 儲存體的共用存取簽章， (SAS) 提供每個 blob 的讀取權限。 另請注意，使用模糊的字串 (`h` 字串值前面的) ，以確保永遠不會記錄 SAS。

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

下一個範例是從 Azure Data Lake Storage Gen 2 (ADLSv2) 內嵌資料。 此處使用的認證 (`...`) 是 (共用金鑰) 的儲存體帳號憑證，而且我們只會針對連接字串的秘密部分使用字串混淆。

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;...'
)
```

下一個範例會從 Azure Data Lake Storage (ADLS) 內嵌單一檔案。
它會使用使用者的認證來存取 ADLS (，因此不需要將儲存體 URI 視為包含秘密) 。 它也會說明如何指定內嵌屬性。

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```
