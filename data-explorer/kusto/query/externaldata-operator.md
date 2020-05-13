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
ms.openlocfilehash: d46a8669c523955f74d3f489c7b10e5b0f7ccef6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373293"
---
# <a name="externaldata-operator"></a>externaldata 運算子

運算子會傳回 `externaldata` 一個資料表，其架構是在查詢本身中定義，而且其資料是從外部儲存成品（例如 Azure Blob 儲存體中的 blob）讀取。

> [!NOTE]
> 此運算子沒有管線輸入。

**語法**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *Value1* [ `,` ...] `)` ]

**引數**

* *ColumnName*、 *ColumnType*：定義資料表的架構。
  語法與在[. create table](../management/create-table-command.md)中定義資料表時所使用的語法相同。

* *StorageConnectionString*：[儲存體連接字串](../api/connection-strings/storage.md)會描述保存要傳回之資料的儲存體成品。

* *Prop1*， *Value1*，...：描述如何解讀從儲存體取得之資料的其他屬性，如 [內嵌[屬性](../management/data-ingestion/index.md)] 底下所列。
    * 目前支援的屬性： `format` 和 `ignoreFirstRecord` 。
    * 支援的資料格式：支援任何內嵌[資料格式](../../ingestion-supported-formats.md)，包括 `csv` 、 `tsv` 、 `json` 、 `parquet` 、 `avro` 。

**傳回**

`externaldata`運算子會傳回給定架構的資料表，其資料是從儲存體連接字串所指示的指定儲存體成品進行剖析。

**範例**

下列範例顯示如何在資料表中尋找資料 `UserID` 行屬於一組已知識別碼的所有記錄（每行一個），並保留在外部 blob 中。
因為此集合是由查詢間接參考，所以可能非常大。

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```