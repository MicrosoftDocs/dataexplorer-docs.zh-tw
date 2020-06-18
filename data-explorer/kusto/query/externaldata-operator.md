---
title: externaldata 運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 externaldata 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: f8878a3c4589dca3cfacf935a787e8c754ab3ede
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84942670"
---
# <a name="externaldata-operator"></a>externaldata 運算子

運算子會傳回 `externaldata` 一個資料表，其架構是在查詢本身中定義，而且其資料是從外部儲存成品（例如 Azure Blob 儲存體中的 blob）讀取。

**語法**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Value1* [ `,` ...] `)` ]

**引數**

* *ColumnName*、 *ColumnType*：定義資料表的架構。
  語法與在[. create table](../management/create-table-command.md)中定義資料表時所使用的語法相同。

* *StorageConnectionString*：[儲存體連接字串](../api/connection-strings/storage.md)會描述保存要傳回之資料的儲存體成品。

* *Prop1*， *Value1*，...：描述如何解讀從儲存體取得之資料的其他屬性，如 [內嵌[屬性](../../ingestion-properties.md)] 底下所列。
    * 目前支援的屬性： `format` 和 `ignoreFirstRecord` 。
    * 支援的資料格式：支援任何內嵌[資料格式](../../ingestion-supported-formats.md)，包括 `CSV` 、 `TSV` 、 `JSON` 、 `Parquet` 、 `Avro` 。

> [!NOTE]
> 此運算子沒有管線輸入。

**傳回**

`externaldata`運算子會傳回給定架構的資料表，其資料是從儲存體連接字串所指示的指定儲存體成品進行剖析。

**範例**

下列範例顯示如何在資料表中尋找資料 `UserID` 行屬於一組已知識別碼的所有記錄（每行一個），並保留在外部 blob 中。
因為此集合是由查詢間接參考，所以可能會很大。

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

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

上述範例可視為快速查詢多個資料檔案，而不需定義[外部資料表](schema-entities/externaltables.md)的方式。 

>[!NOTE]
>運算子無法辨識資料分割 `externaldata()` 。
