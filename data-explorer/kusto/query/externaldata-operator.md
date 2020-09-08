---
title: externaldata 操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的外部資料操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 8bb30180a7506b594e5747e3591f0d1aff80f8c3
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557355"
---
# <a name="externaldata-operator"></a>externaldata 運算子

運算子會傳回 `externaldata` 資料表，其架構是在查詢本身定義，而其資料是從外部儲存體成品讀取，例如 Azure Blob 儲存體中的 blob 或 Azure Data Lake Storage 中的檔案。

## <a name="syntax"></a>語法

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...]`)`   
`[`*StorageConnectionString* [ `,` ...]`]`   
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

## <a name="arguments"></a>引數

* *ColumnName*、 *ColumnType*：引數會定義資料表的架構。
  語法與在 [. create table](../management/create-table-command.md)中定義資料表時所使用的語法相同。

* *StorageConnectionString*： [儲存體連接字串](../api/connection-strings/storage.md) ，會描述保存要傳回之資料的儲存體成品。

* *PropertyName*、 *PropertyValue*、...：其他屬性，這些屬性會描述如何解讀從儲存體取出的資料，如 [內嵌 [屬性](../../ingestion-properties.md)] 下所列。

目前支援的屬性為：

| 屬性         | 類型     | 描述       |
|------------------|----------|-------------------|
| `format`         | `string` | 資料格式。 如果未指定，則會嘗試從副檔名偵測資料格式 (預設為 `CSV`) 。 支援任何內嵌 [資料格式](../../ingestion-supported-formats.md) 。 |
| `ignoreFirstRecord` | `bool` | 如果設定為 true，表示忽略每個檔案中的第一筆記錄。 使用標頭查詢 CSV 檔案時，這個屬性很有用。 |
| `ingestionMapping` | `string` | 字串值，指出如何將來源檔案中的資料對應至運算子結果集中的實際資料行。 請參閱[資料對應](../management/mappings.md)。 |


> [!NOTE]
> * 這個運算子不接受任何管線輸入。
> * 標準 [查詢限制](../concepts/querylimits.md) 也適用于外部資料查詢。

## <a name="returns"></a>傳回

`externaldata`運算子會傳回指定架構的資料表，其資料已從指定的儲存體成品進行剖析，以儲存體連接字串表示。

## <a name="examples"></a>範例

**提取儲存在 Azure Blob 儲存體中的使用者識別碼清單**

下列範例示範如何在資料表中尋找資料 `UserID` 行屬於已知識別碼集的所有記錄，並保留 (外部儲存體檔案中的每一行) 。 因為未指定資料格式，所以偵測到的資料格式為 `TXT` 。

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" 
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

**查詢多個資料檔案**

下列範例會查詢儲存在外部儲存體中的多個資料檔案。

```kusto
externaldata(Timestamp:datetime, ProductId:string, ProductDescription:string)
[
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/03/part-00000-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz?...SAS..."
]
with(format="csv")
| summarize count() by ProductId
```

您可以將上述範例視為在不定義 [外部資料表](schema-entities/externaltables.md)的情況下，查詢多個資料檔案的快速方法。

> [!NOTE]
> 操作員無法辨識資料分割 `externaldata` 。

**查詢階層式資料格式**

若要查詢階層式資料格式，例如、、 `JSON` `Parquet` `Avro` 或 `ORC` ， `ingestionMapping` 必須在運算子屬性中指定。 在此範例中，有一個儲存在 Azure Blob 儲存體中的 JSON 檔案，其中包含下列內容：

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

若要使用運算子來查詢這個檔案 `externaldata` ，必須指定資料對應。 對應會決定如何將 JSON 欄位對應到運算子結果集資料行：

```kusto
externaldata(Timestamp: datetime, TenantId: guid, MethodName: string)
[ 
   h@'https://mycompanystorage.blob.core.windows.net/events/2020/09/01/part-0000046c049c1-86e2-4e74-8583-506bda10cca8.json?...SAS...'
]
with(format='multijson', ingestionMapping='[{"Column":"Timestamp","Properties":{"Path":"$.time"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]')
```

此 `MultiJSON` 格式是在這裡使用，因為單一 JSON 記錄會橫跨多行。

如需對應語法的詳細資訊，請參閱 [資料](../management/mappings.md)對應。
