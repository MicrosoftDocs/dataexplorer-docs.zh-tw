---
title: .ingest 到命令(從存儲中提取數據) - Azure 數據資源管理器 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 .ingest 到命令(從存儲中提取數據)。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 1f304f9dc064094c6d55cb32f3fb453f32114979
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521432"
---
# <a name="the-ingest-into-command-pull-data-from-storage"></a>.ingest 到指令(從儲存中提取資料)

該`.ingest into`命令通過從一個或多個雲端儲存專案「提取」數據來將數據引入表中。
例如,該命令可以從 Azure Blob 儲存中檢索 1000 個 CSV 格式的 Blob,對其進行分析,並將它們一起引入單個目標表。
數據將追加到表中,而不會影響現有記錄,並且不修改表的架構。

**語法**

`.ingest`[`async` `into` `with` `(` `,` `=`*IngestionPropertyValue**TableName**SourceDataLocator* *IngestionPropertyName*] 表名 來源資料定位器 ( 引入屬性名稱 引入屬性值 ] ...] `table``)`]

**引數**

* `async`:如果指定,該命令將立即返回,並繼續在後台引入。 該命令的結果將包括一個`OperationId`值,該值隨後可用於該`.show operation`命令以檢索引入完成狀態和結果。
  
* *表名稱*:要將資料引入到的表的名稱。
  表名稱始終與上下文中的資料庫相關,並且其架構是如果未提供架構映射物件,則將為數據假定的架構。

* *SourceDataLocator:*`string`類型 的文字`(`,`)`或由 和字元包圍的此類文本的逗號分隔清單,指示包含要提取數據的儲存工件。 請參考[儲存連線字串](../../api/connection-strings/storage.md)。

> [!NOTE]
> 強烈建議對包含實際認證的*SourceDataPointer*使用[模糊字串文字](../../query/scalar-data-types/string.md#obfuscated-string-literals)。
> 該服務將一定要在其內部跟蹤、錯誤消息等中擦除憑據。

* *引入屬性名稱*,*引入屬性值*:影響引入過程的任何數量的[引入屬性](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)。

**結果**

該命令的結果是具有與命令生成的數據分片("範圍")一樣多的記錄的表。
如果未生成數據分片,則返回單個記錄,並返回空(零值)範圍ID。

|名稱       |類型      |描述                                                                |
|-----------|----------|---------------------------------------------------------------------------|
|放大縮小字型功能 放大縮小字型功能   |`guid`    |命令產生的數據分片的唯一標識符。|
|專案已載入 |`string`  |與此記錄相關的一個或多個存儲專案。             |
|Duration   |`timespan`|執行攝入需要多長時間。                                     |
|有錯誤  |`bool`    |此記錄是否表示引入失敗。                |
|操作代碼|`guid`    |表示操作的唯一 ID。 可與命令一`.show operation`起使用。|

**備註**

此命令不修改要引入的表的架構。
如有必要,數據在引入過程中被「強制」到此架構中,而不是相反(忽略額外的列,缺少的列被視為空值)。

**範例**

下一個範例指示引擎將 Azure Blob 儲存中的兩個 Blob 讀取為`T`CSV 檔,並將其內容引入到表中。 表示`...`Azure 儲存共享存取簽名 (SAS),它允許讀取存取每個 Blob。 另請注意,使用模糊字串(`h`字串值前面)以確保永遠不會記錄 SAS。

```kusto
.ingest into table T (
    h'https://contoso.blob.core.windows.net/container/file1.csv?...',
    h'https://contoso.blob.core.windows.net/container/file2.csv?...'
)
```

下一個範例是從 Azure 資料儲存第 2 代 (ADLSv2) 中引入數據。 此處使用的認證 (`...`) 是儲存帳戶認證 (共用金鑰),我們僅對連接字串的機密部分使用字串混淆。

```kusto
.ingest into table T (
  'abfss://myfilesystem@contoso.dfs.core.windows.net/path/to/file1.csv;'
    h'...'
)
```

下一個範例從 Azure 資料儲存 (ADLS) 中引入單個檔。
它使用使用者的認證存取 ADLS(因此無需將儲存 URI 視為包含機密)。 它還演示如何指定引入屬性。

```kusto
.ingest into table T ('adl://contoso.azuredatalakestore.net/Path/To/File/file1.ext;impersonate')
  with (format='csv')
```

