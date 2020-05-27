---
title: Azure 資料總管的資料內嵌屬性
description: 瞭解 Azure 資料總管支援的各種資料內嵌屬性。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 25e80458dc4f0432e0f9e4c385fb71c4b8bf3997
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011562"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Azure 資料總管資料內嵌屬性 

資料內嵌是將資料新增至資料表，並可在 Azure 資料總管中進行查詢的程式。 您可以在關鍵字之後，將屬性新增至內嵌命令 `with` 。

## <a name="ingestion-properties"></a>內嵌屬性

下表列出 Azure 資料總管支援的屬性、加以說明，並提供範例： 

|屬性              |描述                                              |範例                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |字串值，該值指出如何將來源檔案的資料對應到資料表中的實際資料行。 定義 `format` 具有相關對應類型的值。 請參閱[資料對應](kusto/management/mappings.md)。|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(已淘汰： `avroMapping`、`csvMapping`、`jsonMapping`) |
|`ingestionMappingReference`|字串值，該值指出如何使用具名對應原則物件，將來源檔案的資料對應到資料表中的實際資料行。 定義 `format` 具有相關對應類型的值。 請參閱[資料對應](kusto/management/mappings.md)。|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(已淘汰： `avroMappingReference`、`csvMappingReference`、`jsonMappingReference`)|
|`creationTime` |要在內嵌資料範圍建立時使用的 datetime 值（格式化為 ISO8601 字串）。 如未指定，則會使用目前的值 (`now()`)。 當內嵌較舊的資料時，覆寫預設值會很有用，因此會正確套用保留原則。|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|布林值，若已指定，則指示命令擴充資料表的結構描述 (預設為 `false`)。 此選項僅適用於 `.append` 和 `.set-or-append` 命令。 唯一允許的結構描述擴充會在資料表結尾新增其他資料行。|如果原始資料表架構為 `(a:string, b:int)` ，有效的架構延伸會是 `(a:string, b:int, c:datetime, d:string)` ，但 `(a:string, c:datetime)` 無效|
|`folder` |用於內嵌[查詢](kusto/management/data-ingestion/ingest-from-query.md)命令，要指派給資料表的資料夾。 如果資料表已經存在，這個屬性將會覆寫資料表的資料夾。|`with (folder="Tables/Temporary")`|
|`format` |資料格式（請參閱[支援的資料格式](ingestion-supported-formats.md)）。|`with (format="csv")`|
|`ingestIfNotExists`|字串值，若已指定，則會在資料表中已經有具相同值和 `ingest-by:` 標記的資料時，防止從後續內嵌。 如此可確保資料以等冪方式擷取。 如需詳細資訊，請參閱內嵌[：標記](kusto/management/extents-overview.md#ingest-by-extent-tags)。|屬性 `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` 表示如果具有標記的資料 `ingest-by:Part0001` 已經存在，則不會完成目前的內嵌。 如果它還不存在，這項新的內嵌應該會設定此標記（如果未來的內嵌嘗試再次內嵌相同的資料）。|
|`ignoreFirstRecord` |布林值，若設為 `true`，則表示內嵌應該忽略每個檔案的第一筆記錄。 如果檔案中的 `CSV` 第一筆記錄是資料行名稱，這個屬性適用于和類似格式的檔案。 根據預設， `false` 會假設為。|`with (ignoreFirstRecord=false)`|
|`persistDetails` |布林值，若已指定，則表示命令應該保存詳細的結果 (即使成功)，以便 [.show operation details](kusto/management/operations.md#show-operation-details) 命令擷取它們。 預設為 `false`。|`with (persistDetails=true)`|
|`policy_ingestiontime`|布林值，若已指定，則描述是否要在此命令所建立的資料表上啟用[內嵌時間](kusto/management/ingestiontimepolicy.md)原則。 預設值為 `true`。|`with (policy_ingestiontime=false)`|
|`recreate_schema` |布林值，若已指定，則描述此命令是否可以重建資料表的結構描述。 此屬性只適用于 `.set-or-replace` 命令。 如果同時設定了這兩個屬性，則此屬性優先于 `extend_schema` 屬性。|`with (recreate_schema=true)`|
|`tags`|要與內嵌資料產生關聯的[標記](kusto/management/extents-overview.md#extent-tagging)清單，格式為 JSON 字串 |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|指出要在內嵌期間執行哪些驗證的 JSON 字串。 如需不同選項的說明，請參閱[資料](ingest-data-overview.md)內嵌。| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`（這實際上是預設原則）|
|`zipPattern`|從具有 ZIP 封存的儲存體內嵌資料時，請使用此屬性。 這是一個字串值，表示在選取要內嵌的 ZIP 封存中的哪些檔案時，所要使用的正則運算式。  封存中的所有其他檔案都會被忽略。|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>後續步驟

* 深入瞭解[資料](ingest-data-overview.md)內嵌
* 深入瞭解[支援的資料格式](ingestion-supported-formats.md)
